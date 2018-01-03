---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-6
title: "Создание клиента JavaScript | Документы Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/16/2014
ms.topic: article
ms.assetid: 20360326-b123-4b1e-abae-1d350edf4ce4
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-6
msc.type: authoredcontent
ms.openlocfilehash: b397c5a413ae213c9b79da1c0e0626efe21c7e21
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
---
<a name="create-the-javascript-client"></a><span data-ttu-id="b65b1-102">Создание клиента JavaScript</span><span class="sxs-lookup"><span data-stu-id="b65b1-102">Create the JavaScript Client</span></span>
====================
<span data-ttu-id="b65b1-103">по [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="b65b1-103">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

[<span data-ttu-id="b65b1-104">Загрузка завершенного проекта</span><span class="sxs-lookup"><span data-stu-id="b65b1-104">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

<span data-ttu-id="b65b1-105">В этом разделе вы создадите клиент для приложения, с помощью HTML, JavaScript и [Knockout.js](http://knockoutjs.com/) библиотеки.</span><span class="sxs-lookup"><span data-stu-id="b65b1-105">In this section, you will create the client for the application, using HTML, JavaScript, and the [Knockout.js](http://knockoutjs.com/) library.</span></span> <span data-ttu-id="b65b1-106">Мы создадим клиентское приложение выполняется на этапах:</span><span class="sxs-lookup"><span data-stu-id="b65b1-106">We'll build the client app in stages:</span></span>

- <span data-ttu-id="b65b1-107">Отображение списка книг.</span><span class="sxs-lookup"><span data-stu-id="b65b1-107">Showing a list of books.</span></span>
- <span data-ttu-id="b65b1-108">Отображение сведений книги.</span><span class="sxs-lookup"><span data-stu-id="b65b1-108">Showing a book detail.</span></span>
- <span data-ttu-id="b65b1-109">Добавление новой книги.</span><span class="sxs-lookup"><span data-stu-id="b65b1-109">Adding a new book.</span></span>

<span data-ttu-id="b65b1-110">Библиотека маскирования использует шаблон Model-View-ViewModel (MVVM):</span><span class="sxs-lookup"><span data-stu-id="b65b1-110">The Knockout library uses the Model-View-ViewModel (MVVM) pattern:</span></span>

- <span data-ttu-id="b65b1-111">**Модель** серверные представление данных в бизнес-среде (в нашем случае книг и авторов).</span><span class="sxs-lookup"><span data-stu-id="b65b1-111">The **model** is the server-side representation of the data in the business domain (in our case, books and authors).</span></span>
- <span data-ttu-id="b65b1-112">**Представление** является уровень представления (HTML).</span><span class="sxs-lookup"><span data-stu-id="b65b1-112">The **view** is the presentation layer (HTML).</span></span>
- <span data-ttu-id="b65b1-113">**Модель представления** представляет собой объект JavaScript, который содержит моделей.</span><span class="sxs-lookup"><span data-stu-id="b65b1-113">The **view model** is a JavaScript object that holds the models.</span></span> <span data-ttu-id="b65b1-114">Модель представления — это абстракция код пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b65b1-114">The view model is a code abstraction of the UI.</span></span> <span data-ttu-id="b65b1-115">Он не имеет сведений о представление HTML.</span><span class="sxs-lookup"><span data-stu-id="b65b1-115">It has no knowledge of the HTML representation.</span></span> <span data-ttu-id="b65b1-116">Вместо этого он представляет абстрактные функции, представления, такие как &quot;список книг&quot;.</span><span class="sxs-lookup"><span data-stu-id="b65b1-116">Instead, it represents abstract features of the view, such as &quot;a list of books&quot;.</span></span>

<span data-ttu-id="b65b1-117">Представление данных привязан к модели представления.</span><span class="sxs-lookup"><span data-stu-id="b65b1-117">The view is data-bound to the view model.</span></span> <span data-ttu-id="b65b1-118">Обновления для модели представления автоматически отражаются в представлении.</span><span class="sxs-lookup"><span data-stu-id="b65b1-118">Updates to the view model are automatically reflected in the view.</span></span> <span data-ttu-id="b65b1-119">Модель представления также получает события из представления, например при нажатии кнопки.</span><span class="sxs-lookup"><span data-stu-id="b65b1-119">The view model also gets events from the view, such as button clicks.</span></span>

![](part-6/_static/image1.png)

<span data-ttu-id="b65b1-120">Этот подход упрощает изменение макета и пользовательского интерфейса приложения, может меняться привязок, не переписывая любой код.</span><span class="sxs-lookup"><span data-stu-id="b65b1-120">This approach makes it easy to change the layout and UI of your app, because you can change the bindings, without rewriting any code.</span></span> <span data-ttu-id="b65b1-121">Например, можно показать список элементов, как `<ul>`, затем изменить его позже в таблицу.</span><span class="sxs-lookup"><span data-stu-id="b65b1-121">For example, you might show a list of items as a `<ul>`, then change it later to a table.</span></span>

## <a name="add-the-knockout-library"></a><span data-ttu-id="b65b1-122">Добавьте библиотеку маскирования</span><span class="sxs-lookup"><span data-stu-id="b65b1-122">Add the Knockout Library</span></span>

<span data-ttu-id="b65b1-123">В Visual Studio из **средства** последовательно выберите пункты **диспетчер пакетов библиотеки**.</span><span class="sxs-lookup"><span data-stu-id="b65b1-123">In Visual Studio, from the **Tools** menu, select **Library Package Manager**.</span></span> <span data-ttu-id="b65b1-124">Выберите **консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="b65b1-124">Then select **Package Manager Console**.</span></span> <span data-ttu-id="b65b1-125">В окне консоли диспетчера пакетов введите следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b65b1-125">In the Package Manager Console window, enter the following command:</span></span>

[!code-console[Main](part-6/samples/sample1.cmd)]

<span data-ttu-id="b65b1-126">Эта команда добавляет файлы маскирования папку «скрипты».</span><span class="sxs-lookup"><span data-stu-id="b65b1-126">This command adds the Knockout files to the Scripts folder.</span></span>

## <a name="create-the-view-model"></a><span data-ttu-id="b65b1-127">Создание модели представления</span><span class="sxs-lookup"><span data-stu-id="b65b1-127">Create the View Model</span></span>

<span data-ttu-id="b65b1-128">Добавьте файл JavaScript с именем в файле app.js в папку «скрипты».</span><span class="sxs-lookup"><span data-stu-id="b65b1-128">Add a JavaScript file named app.js to the Scripts folder.</span></span> <span data-ttu-id="b65b1-129">(В обозревателе решений щелкните правой кнопкой мыши папку «скрипты», выберите **добавить**, а затем выберите **файл JavaScript**.) Вставьте следующий код:</span><span class="sxs-lookup"><span data-stu-id="b65b1-129">(In Solution Explorer, right-click the Scripts folder, select **Add**, then select **JavaScript File**.) Paste in the following code:</span></span>

[!code-javascript[Main](part-6/samples/sample2.js)]

<span data-ttu-id="b65b1-130">В Knockout `observable` класс включает привязку данных.</span><span class="sxs-lookup"><span data-stu-id="b65b1-130">In Knockout, the `observable` class enables data-binding.</span></span> <span data-ttu-id="b65b1-131">При изменении содержимого наблюдаемый объект, наблюдаемым уведомляет все элементы управления с привязкой к данным, чтобы они могли обновлять себя.</span><span class="sxs-lookup"><span data-stu-id="b65b1-131">When the contents of an observable change, the observable notifies all of the data-bound controls, so they can update themselves.</span></span> <span data-ttu-id="b65b1-132">( `observableArray` Класс — это версия массива *наблюдаемый объект*.) Прежде всего нашей модели представления состоит из двух наблюдаемые объекты.</span><span class="sxs-lookup"><span data-stu-id="b65b1-132">(The `observableArray` class is the array version of *observable*.) To start with, our view model has two observables:</span></span>

- <span data-ttu-id="b65b1-133">`books`содержит список книг.</span><span class="sxs-lookup"><span data-stu-id="b65b1-133">`books` holds the list of books.</span></span>
- <span data-ttu-id="b65b1-134">`error`содержит сообщение об ошибке при сбое вызова AJAX.</span><span class="sxs-lookup"><span data-stu-id="b65b1-134">`error` contains an error message if an AJAX call fails.</span></span>

<span data-ttu-id="b65b1-135">`getAllBooks` Метод отправляет вызов AJAX, чтобы получить список книг.</span><span class="sxs-lookup"><span data-stu-id="b65b1-135">The `getAllBooks` method makes an AJAX call to get the list of books.</span></span> <span data-ttu-id="b65b1-136">Затем он помещает результат в `books` массива.</span><span class="sxs-lookup"><span data-stu-id="b65b1-136">Then it pushes the result onto the `books` array.</span></span>

<span data-ttu-id="b65b1-137">`ko.applyBindings` Метод входит в состав библиотеки Knockout.</span><span class="sxs-lookup"><span data-stu-id="b65b1-137">The `ko.applyBindings` method is part of the Knockout library.</span></span> <span data-ttu-id="b65b1-138">Он принимает модели представления в качестве параметра, а также задает привязку данных.</span><span class="sxs-lookup"><span data-stu-id="b65b1-138">It takes the view model as a parameter and sets up the data binding.</span></span>

## <a name="add-a-script-bundle"></a><span data-ttu-id="b65b1-139">Добавьте пакет скрипта</span><span class="sxs-lookup"><span data-stu-id="b65b1-139">Add a Script Bundle</span></span>

<span data-ttu-id="b65b1-140">Объединение — это функция в ASP.NET 4.5, упрощающий для объединения или объединить несколько файлов в один файл.</span><span class="sxs-lookup"><span data-stu-id="b65b1-140">Bundling is a feature in ASP.NET 4.5 that makes it easy to combine or bundle multiple files into a single file.</span></span> <span data-ttu-id="b65b1-141">Объединение уменьшает количество запросов на сервер, который может увеличить время загрузки страницы.</span><span class="sxs-lookup"><span data-stu-id="b65b1-141">Bundling reduces the number of requests to the server, which can improve page load time.</span></span>

<span data-ttu-id="b65b1-142">Откройте файл приложения\_Start/BundleConfig.cs.</span><span class="sxs-lookup"><span data-stu-id="b65b1-142">Open the file App\_Start/BundleConfig.cs.</span></span> <span data-ttu-id="b65b1-143">Добавьте следующий код в метод RegisterBundles.</span><span class="sxs-lookup"><span data-stu-id="b65b1-143">Add the following code to the RegisterBundles method.</span></span>

[!code-csharp[Main](part-6/samples/sample3.cs)]

>[!div class="step-by-step"]
<span data-ttu-id="b65b1-144">[Назад](part-5.md)
[Вперед](part-7.md)</span><span class="sxs-lookup"><span data-stu-id="b65b1-144">[Previous](part-5.md)
[Next](part-7.md)</span></span>