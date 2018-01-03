---
title: "Ведение журнала высокой производительности с LoggerMessage в ASP.NET Core"
author: guardrex
description: "Узнайте, как использовать возможности LoggerMessage Создание кэшируемый делегатов, которые требуют меньше распределение объектов, чем методы расширения, средства ведения журнала для сценариев высокой производительности ведения журнала."
ms.author: riande
manager: wpickett
ms.date: 11/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: defba75c6c9ea13d24af4cd8515d82d9e7cf9853
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="9af1e-103">Ведение журнала высокой производительности с LoggerMessage в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9af1e-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

<span data-ttu-id="9af1e-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="9af1e-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9af1e-105">[LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage) возможности создания кэшируемый делегатов, которые требуют меньше распределение объектов и снижение вычислительных ресурсов, чем [методы расширения для средства ведения журнала](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions), такие как `LogInformation`, `LogDebug`и `LogError`.</span><span class="sxs-lookup"><span data-stu-id="9af1e-105">[LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage) features create cacheable delegates that require fewer object allocations and reduced computational overhead than [logger extension methods](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions), such as `LogInformation`, `LogDebug`, and `LogError`.</span></span> <span data-ttu-id="9af1e-106">В сценариях высокого уровня ведения журнала, используйте `LoggerMessage` шаблон.</span><span class="sxs-lookup"><span data-stu-id="9af1e-106">For high-performance logging scenarios, use the `LoggerMessage` pattern.</span></span>

<span data-ttu-id="9af1e-107">`LoggerMessage`предоставляет следующие преимущества по сравнению с методами расширения средство ведения журнала производительности:</span><span class="sxs-lookup"><span data-stu-id="9af1e-107">`LoggerMessage` provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="9af1e-108">Методы расширения для средства ведения журнала требуется типы значений «упаковкой» (преобразование), например `int`, в `object`.</span><span class="sxs-lookup"><span data-stu-id="9af1e-108">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="9af1e-109">`LoggerMessage` Шаблон позволяет избежать упаковка-преобразование с помощью статического `Action` поля и методы расширения со строгой типизацией параметров.</span><span class="sxs-lookup"><span data-stu-id="9af1e-109">The `LoggerMessage` pattern avoids boxing by using static `Action` fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="9af1e-110">Методы расширения для средства ведения журнала необходимо выполнить синтаксический анализ шаблона сообщения (строка форматирования именованный), каждый раз записывается сообщение журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-110">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="9af1e-111">`LoggerMessage`требуется только синтаксическом анализе шаблона один раз при определении сообщения.</span><span class="sxs-lookup"><span data-stu-id="9af1e-111">`LoggerMessage` only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="9af1e-112">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9af1e-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9af1e-113">В примере приложения показано `LoggerMessage` возможности с помощью системы отслеживания основные предложения.</span><span class="sxs-lookup"><span data-stu-id="9af1e-113">The sample app demonstrates `LoggerMessage` features with a basic quote tracking system.</span></span> <span data-ttu-id="9af1e-114">Добавляет и удаляет кавычки использует базу данных в памяти приложения.</span><span class="sxs-lookup"><span data-stu-id="9af1e-114">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="9af1e-115">При возникновении этих операций, сообщения журнала создаются с помощью `LoggerMessage` шаблон.</span><span class="sxs-lookup"><span data-stu-id="9af1e-115">As these operations occur, log messages are generated using the `LoggerMessage` pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="9af1e-116">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="9af1e-116">LoggerMessage.Define</span></span>

<span data-ttu-id="9af1e-117">[Определение (LogLevel EventId, String)](/dotnet/api/microsoft.extensions.logging.loggermessage.define) создает `Action` делегат для ведения журнала сообщений.</span><span class="sxs-lookup"><span data-stu-id="9af1e-117">[Define(LogLevel, EventId, String)](/dotnet/api/microsoft.extensions.logging.loggermessage.define) creates an `Action` delegate for logging a message.</span></span> <span data-ttu-id="9af1e-118">`Define`перегрузки позволяют передача до шести параметров типа именованного формата строки (шаблона).</span><span class="sxs-lookup"><span data-stu-id="9af1e-118">`Define` overloads permit passing up to six type parameters to a named format string (template).</span></span>

## <a name="loggermessagedefinescope"></a><span data-ttu-id="9af1e-119">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="9af1e-119">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="9af1e-120">[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) создает `Func` делегата для определения [входа области](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="9af1e-120">[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) creates a `Func` delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="9af1e-121">`DefineScope`перегрузки позволяют передача до трех параметров типа именованного формата строки (шаблона).</span><span class="sxs-lookup"><span data-stu-id="9af1e-121">`DefineScope` overloads permit passing up to three type parameters to a named format string (template).</span></span>

## <a name="message-template-named-format-string"></a><span data-ttu-id="9af1e-122">Шаблон сообщения (с именем строка форматирования)</span><span class="sxs-lookup"><span data-stu-id="9af1e-122">Message template (named format string)</span></span>

<span data-ttu-id="9af1e-123">Строка, предоставленная для `Define` и `DefineScope` методов, шаблон и не интерполированную строку.</span><span class="sxs-lookup"><span data-stu-id="9af1e-123">The string provided to the `Define` and `DefineScope` methods is a template and not an interpolated string.</span></span> <span data-ttu-id="9af1e-124">Заполнители заполняются в том порядке, в котором были указаны типы.</span><span class="sxs-lookup"><span data-stu-id="9af1e-124">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="9af1e-125">Имена заполнителей в шаблоне должны быть описательным и согласованное на шаблоны.</span><span class="sxs-lookup"><span data-stu-id="9af1e-125">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="9af1e-126">Они служат в качестве имен свойств в структурированных данных журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-126">They serve as property names within structured log data.</span></span> <span data-ttu-id="9af1e-127">Мы рекомендуем [Pascal регистр](/dotnet/standard/design-guidelines/capitalization-conventions) имен заполнителя.</span><span class="sxs-lookup"><span data-stu-id="9af1e-127">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="9af1e-128">Например `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="9af1e-128">For example, `{Count}`, `{FirstName}`.</span></span>

## <a name="implementing-loggermessagedefine"></a><span data-ttu-id="9af1e-129">Реализация LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="9af1e-129">Implementing LoggerMessage.Define</span></span>

<span data-ttu-id="9af1e-130">Каждое сообщение журнала является `Action` , которые содержатся в статического поля, созданные `LoggerMessage.Define`.</span><span class="sxs-lookup"><span data-stu-id="9af1e-130">Each log message is an `Action` held in a static field created by `LoggerMessage.Define`.</span></span> <span data-ttu-id="9af1e-131">Например, в пример приложения создает поле для описания сообщения журнала для запроса GET для страницы индекса (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="9af1e-131">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="9af1e-132">Для `Action`, укажите:</span><span class="sxs-lookup"><span data-stu-id="9af1e-132">For the `Action`, specify:</span></span>

* <span data-ttu-id="9af1e-133">Уровень ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-133">The log level.</span></span>
* <span data-ttu-id="9af1e-134">Уникальный идентификатор события ([EventId](/dotnet/api/microsoft.extensions.logging.eventid)) с именем расширения статического метода.</span><span class="sxs-lookup"><span data-stu-id="9af1e-134">A unique event identifier ([EventId](/dotnet/api/microsoft.extensions.logging.eventid)) with the name of the static extension method.</span></span>
* <span data-ttu-id="9af1e-135">Шаблон сообщения (с именем строка форматирования).</span><span class="sxs-lookup"><span data-stu-id="9af1e-135">The message template (named format string).</span></span> 

<span data-ttu-id="9af1e-136">Запрос страницы индекса наборов выборки приложения:</span><span class="sxs-lookup"><span data-stu-id="9af1e-136">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="9af1e-137">Уровень для ведения журнала `Information`.</span><span class="sxs-lookup"><span data-stu-id="9af1e-137">Log level to `Information`.</span></span>
* <span data-ttu-id="9af1e-138">Идентификатор события, `1` именем `IndexPageRequested` метод.</span><span class="sxs-lookup"><span data-stu-id="9af1e-138">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="9af1e-139">Шаблон сообщения (с именем строка форматирования) в строку.</span><span class="sxs-lookup"><span data-stu-id="9af1e-139">Message template (named format string) to a string.</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="9af1e-140">Структурированный ведения журнала хранилища воспользоваться имя события прилагается идентификатор события для обогащения ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-140">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="9af1e-141">Например [Serilog](https://github.com/serilog/serilog-extensions-logging) использует имя события.</span><span class="sxs-lookup"><span data-stu-id="9af1e-141">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="9af1e-142">`Action` Вызывается с помощью метода расширения на строго типизированными.</span><span class="sxs-lookup"><span data-stu-id="9af1e-142">The `Action` is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="9af1e-143">`IndexPageRequested` Метод заносит в журнал сообщение для запроса на получение страницы индекса в пример приложения:</span><span class="sxs-lookup"><span data-stu-id="9af1e-143">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="9af1e-144">`IndexPageRequested`вызывается для средства ведения журнала в `OnGetAsync` метод в *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="9af1e-144">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="9af1e-145">Проверьте приложение вывод на консоль:</span><span class="sxs-lookup"><span data-stu-id="9af1e-145">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="9af1e-146">Чтобы передать параметры в сообщение журнала, определите до шести типов при создании статического поля.</span><span class="sxs-lookup"><span data-stu-id="9af1e-146">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="9af1e-147">Пример приложения регистрирует строка при добавлении предложения, определив `string` тип для `Action` поля:</span><span class="sxs-lookup"><span data-stu-id="9af1e-147">The sample app logs a string when adding a quote by defining a `string` type for the `Action` field:</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="9af1e-148">Шаблон сообщения журнала делегата получает его значения заполнителей из типов, предоставляемых.</span><span class="sxs-lookup"><span data-stu-id="9af1e-148">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="9af1e-149">Пример приложения определяет делегат для добавления предложения параметр квоты которого `string`:</span><span class="sxs-lookup"><span data-stu-id="9af1e-149">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="9af1e-150">Метод статического расширения для добавления предложения, `QuoteAdded`, получает значения аргумента квоты и передает их в `Action` делегировать:</span><span class="sxs-lookup"><span data-stu-id="9af1e-150">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the `Action` delegate:</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="9af1e-151">В файле кода страницы индекса (*Pages/Index.cshtml.cs*), `QuoteAdded` вызывается в журнал сообщение:</span><span class="sxs-lookup"><span data-stu-id="9af1e-151">In the Index page's code-behind file (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="9af1e-152">Проверьте приложение вывод на консоль:</span><span class="sxs-lookup"><span data-stu-id="9af1e-152">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="9af1e-153">В образце приложения реализуется `try` &ndash; `catch` шаблон для удаления квоты.</span><span class="sxs-lookup"><span data-stu-id="9af1e-153">The sample app implements a `try`&ndash;`catch` pattern for quote deletion.</span></span> <span data-ttu-id="9af1e-154">Для операции удаления успешно записывается информационное сообщение.</span><span class="sxs-lookup"><span data-stu-id="9af1e-154">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="9af1e-155">Сообщение об ошибке регистрируется для операции удаления, когда возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="9af1e-155">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="9af1e-156">Сообщение журнала для неудачной операции удаления включает трассировку стека исключения (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="9af1e-156">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="9af1e-157">Обратите внимание на то, как исключение передается в делегат `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="9af1e-157">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="9af1e-158">В индекс страницы кода, вызывает удаление успешно Квота `QuoteDeleted` метод для средства ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-158">In the Index page code-behind, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="9af1e-159">Если не найдены знак кавычек для удаления, `ArgumentNullException` возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="9af1e-159">When a quote isn't found for deletion, an `ArgumentNullException` is thrown.</span></span> <span data-ttu-id="9af1e-160">Перехватить исключение в приложении `try` &ndash; `catch` инструкции и регистрируется путем вызова `QuoteDeleteFailed` метод средство ведения журнала в `catch` блока (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="9af1e-160">The exception is trapped by the `try`&ndash;`catch` statement and logged by calling the `QuoteDeleteFailed` method on the logger in the `catch` block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="9af1e-161">При успешном удалении знак кавычек, проверьте приложение вывод на консоль:</span><span class="sxs-lookup"><span data-stu-id="9af1e-161">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="9af1e-162">При сбое удаления квоты, проверьте приложение вывод на консоль.</span><span class="sxs-lookup"><span data-stu-id="9af1e-162">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="9af1e-163">Обратите внимание, что исключения будут включены в сообщение журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-163">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T](T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() in 
      <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="implementing-loggermessagedefinescope"></a><span data-ttu-id="9af1e-164">Реализация LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="9af1e-164">Implementing LoggerMessage.DefineScope</span></span>

<span data-ttu-id="9af1e-165">Определение [входа области](xref:fundamentals/logging/index#log-scopes) для применения в серии сообщений журнала с помощью [DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) метод.</span><span class="sxs-lookup"><span data-stu-id="9af1e-165">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the [DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) method.</span></span>

<span data-ttu-id="9af1e-166">Пример приложения имеет **очистить все** кнопку для удаления всех квот в базе данных.</span><span class="sxs-lookup"><span data-stu-id="9af1e-166">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="9af1e-167">Кавычки будут удалены, удалив один одновременно.</span><span class="sxs-lookup"><span data-stu-id="9af1e-167">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="9af1e-168">Каждый раз, удаляется знак кавычек, `QuoteDeleted` метод будет вызван на средство ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-168">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="9af1e-169">Область журнала добавляется в эти сообщения журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-169">A log scope is added to these log messages.</span></span>

<span data-ttu-id="9af1e-170">Включить `IncludeScopes` в параметры средства ведения журнала консоли:</span><span class="sxs-lookup"><span data-stu-id="9af1e-170">Enable `IncludeScopes` in the console logger options:</span></span>

[!code-csharp[Main](loggermessage/sample/Program.cs?name=snippet1&highlight=22)]

<span data-ttu-id="9af1e-171">Параметр `IncludeScopes` требуется для включения областей журнала в приложениях ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="9af1e-171">Setting `IncludeScopes` is required in ASP.NET Core 2.0 apps to enable log scopes.</span></span> <span data-ttu-id="9af1e-172">Установка `IncludeScopes` через *appsettings* файлы конфигурации — это компонент, который запланировал для версии ASP.NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="9af1e-172">Setting `IncludeScopes` via *appsettings* configuration files is a feature that's planned for the ASP.NET Core 2.1 release.</span></span>

<span data-ttu-id="9af1e-173">Пример приложения очищает других поставщиков и добавляет фильтры, чтобы уменьшить выходные данные журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-173">The sample app clears other providers and adds filters to reduce the logging output.</span></span> <span data-ttu-id="9af1e-174">Это упрощает см. пример сообщений журнала, которые демонстрируют `LoggerMessage` функции.</span><span class="sxs-lookup"><span data-stu-id="9af1e-174">This makes it easier to see the sample's log messages that demonstrate `LoggerMessage` features.</span></span>

<span data-ttu-id="9af1e-175">Для создания области журнала, добавьте поле для хранения `Func` делегата для области.</span><span class="sxs-lookup"><span data-stu-id="9af1e-175">To create a log scope, add a field to hold a `Func` delegate for the scope.</span></span> <span data-ttu-id="9af1e-176">Пример приложения создает поле с именем `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="9af1e-176">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="9af1e-177">Используйте `DefineScope` для создания делегата.</span><span class="sxs-lookup"><span data-stu-id="9af1e-177">Use `DefineScope` to create the delegate.</span></span> <span data-ttu-id="9af1e-178">Три типа можно указать до для использования как аргументы шаблонов при вызове делегата.</span><span class="sxs-lookup"><span data-stu-id="9af1e-178">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="9af1e-179">Пример приложения используется шаблон сообщений, который включает в себя количество удаленных квот ( `int` типа):</span><span class="sxs-lookup"><span data-stu-id="9af1e-179">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="9af1e-180">Предусмотрите статических расширения сообщение журнала.</span><span class="sxs-lookup"><span data-stu-id="9af1e-180">Provide a static extension method for the log message.</span></span> <span data-ttu-id="9af1e-181">Включать любые параметры типа для именованных свойств, отображаемых в шаблоне сообщения.</span><span class="sxs-lookup"><span data-stu-id="9af1e-181">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="9af1e-182">Пример приложения принимает `count` кавычки для удаления и возвращает `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="9af1e-182">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="9af1e-183">Вызывает модуль ведения журнала в качестве оболочки для области `using` блока:</span><span class="sxs-lookup"><span data-stu-id="9af1e-183">The scope wraps the logging extension calls in a `using` block:</span></span>

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="9af1e-184">Проверьте сообщения журнала в приложение вывод на консоль.</span><span class="sxs-lookup"><span data-stu-id="9af1e-184">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="9af1e-185">Следующий результат показывает три кавычки удалена с сообщением области журнала включено:</span><span class="sxs-lookup"><span data-stu-id="9af1e-185">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

## <a name="see-also"></a><span data-ttu-id="9af1e-186">См. также</span><span class="sxs-lookup"><span data-stu-id="9af1e-186">See also</span></span>

* [<span data-ttu-id="9af1e-187">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="9af1e-187">Logging</span></span>](xref:fundamentals/logging/index)