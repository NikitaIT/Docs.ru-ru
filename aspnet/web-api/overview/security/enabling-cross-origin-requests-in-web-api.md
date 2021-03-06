---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: Включение запросов о происхождении в ASP.NET Web API 2 | Документация Майкрософт
author: MikeWasson
description: Показано, как для поддержки общего доступа к независимо от источника (CORS) в веб-API ASP.NET.
ms.author: riande
ms.date: 07/15/2014
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: eddf61a4468807f5efd658438c1c27a1d2f9c486
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41836627"
---
<a name="enabling-cross-origin-requests-in-aspnet-web-api-2"></a>Включение запросов о происхождении в ASP.NET Web API 2
====================
по [Майк Уоссон](https://github.com/MikeWasson)

> Безопасность обозревателя запрещает отправку запросов AJAX в другой домен веб-страницы. Это ограничение называется *политика одного источника*и предотвращает чтение конфиденциальных данных с другого сайта вредоносный сайт. Тем не менее иногда вам может потребоваться разрешить другие сайты вызова веб-API.
> 
> [Кросс-Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) — это стандарт консорциума W3C, позволяющий серверу смягчить ограничения политики одного источника. С помощью CORS сервер может явным образом разрешить некоторые запросы независимо от источника а другие — отклонять. CORS — более безопасное и более гибким, чем предыдущие технологии, такие как [JSONP](http://en.wikipedia.org/wiki/JSONP). Этом руководстве показано, как включить поддержку CORS в приложении веб-API.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Версии программного обеспечения, используемые в этом руководстве
> 
> 
> - [Visual Studio 2013 с обновлением 2](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - Веб-API 2.2


<a id="intro"></a>
## <a name="introduction"></a>Вступление

В этом учебнике показано, что поддержка CORS в веб-API ASP.NET. Мы начнем с создания двух проектов ASP.NET — один вызываемой «веб-служба», где размещен контроллер Web API, и другие вызываемой «WebClient», который вызывает веб-службы. Так как оба приложения размещены в разных доменах, AJAX-запрос из веб-клиента для веб-службы — это запрос независимо от источника.

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a>Что такое «Того же происхождения»?

Два URL-адреса иметь того же происхождения, если они имеют одинаковые схемы, узлов и порты. ([RFC 6454](http://tools.ietf.org/html/rfc6454))

Эти два URL-адреса у того же происхождения:

- `http://example.com/foo.html`
- `http://example.com/bar.html`

Эти URL-адреса имеют различное происхождение по сравнению с предыдущим два:

- `http://example.net` -Другой домен
- `http://example.com:9000/foo.html` -Другой порт
- `https://example.com/foo.html` -Другую схему
- `http://www.example.com/foo.html` -Другой поддомен

> [!NOTE]
> Internet Explorer не учитывает порт при сравнении источников.


<a id="create-webapi-project"></a>
## <a name="create-the-webservice-project"></a>Создание проекта веб-службы

> [!NOTE]
> В этом разделе предполагается, что вы уже умеете создавать проекты веб-API. Если это не так, см. в разделе [Приступая к работе с веб-API ASP.NET](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).


Запустите Visual Studio и создайте новый **веб-приложение ASP.NET** проекта. Выберите **пустой** шаблона проекта. В разделе «Добавление папок и основные ссылки для» выберите **веб-API** флажок. При необходимости выберите параметр «Разместить в облаке», чтобы развернуть приложение в Mircosoft Azure. Корпорация Майкрософт предлагает бесплатного размещения до 10 веб-сайтов в [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).

[![](enabling-cross-origin-requests-in-web-api/_static/image3.png)](enabling-cross-origin-requests-in-web-api/_static/image2.png)

Добавление контроллера веб-API с именем `TestController` следующим кодом:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

Можно запустить приложение локально или развертывать в Azure. (Снимки экрана, в этом руководстве, я развертывается веб-приложениях службы приложений Azure.) Чтобы убедиться, что веб-API работает, перейдите к `http://hostname/api/test/`, где *hostname* — это домен, в котором развертывается приложение. Вы должны увидеть текст ответа, &quot;получить: тестовое сообщение&quot;.

![](enabling-cross-origin-requests-in-web-api/_static/image4.png)

<a id="create-client"></a>
## <a name="create-the-webclient-project"></a>Создание проекта веб-клиента

Создайте другой проект веб-приложения ASP.NET и выберите **MVC** шаблона проекта. При необходимости выберите **изменить способ проверки подлинности** > **без проверки подлинности**. Не требуется проверка подлинности для этого руководства.

[![](enabling-cross-origin-requests-in-web-api/_static/image6.png)](enabling-cross-origin-requests-in-web-api/_static/image5.png)

В обозревателе решений откройте файл Views/Home/Index.cshtml. Замените код в этот файл следующее:

[!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

Для *serviceUrl* переменной, используйте URI веб-службы приложения. Теперь запустите приложение WebClient локально или опубликовать его в другой веб-сайта.

Нажав кнопку «Попробовать» отправляет запрос AJAX в приложение веб-службы, с помощью метода HTTP, перечисленные в раскрывающемся списке (GET, POST или PUT). Это позволит нам проверить различные запросы независимо от источника. Прямо сейчас, приложения веб-служба не поддерживает CORS, поэтому при нажатии кнопки, вы получите ошибку.

![](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> Если смотреть HTTP-трафика в средстве, например [Fiddler](http://www.telerik.com/fiddler), вы увидите, что браузер отправляет запрос GET и запрос выполнен успешно, но вызов AJAX возвращает ошибку. Важно понимать, что политика одного источника не запрещает браузере из *отправки* запроса. Вместо этого он дает приложению просматривать *ответа*.


![](enabling-cross-origin-requests-in-web-api/_static/image8.png)

<a id="enable-cors"></a>
## <a name="enable-cors"></a>Включение CORS

Теперь давайте Включение CORS в приложении веб-службы. Во-первых добавьте пакет CORS NuGet. В Visual Studio из **средства** меню, выберите **диспетчер пакетов библиотеки**, а затем выберите **консоль диспетчера пакетов**. В окне консоли диспетчера пакетов введите следующую команду:

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

Эта команда устанавливает последнюю версию пакета и обновляет все зависимости, включая библиотеки core веб-API. Пользователь флаг версии для определенной версии. CORS пакету требуется веб-API 2.0 или более поздней.

Откройте файл приложения\_Start/WebApiConfig.cs. Добавьте следующий код, чтобы **WebApiConfig.Register** метод.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

Затем добавьте **[EnableCors]** атрибут `TestController` класса:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

Для *источников* параметра, используйте URI, на котором развертывается приложение WebClient. Это позволяет запросов о происхождении из WebClient, по-прежнему запрета на все остальные запросы между доменами. Позже я расскажу о параметры **[EnableCors]** более подробно.

Не используйте косую черту в конце *источников* URL-адрес.

Повторное развертывание обновленного приложения веб-службы. Не нужно обновлять WebClient. Теперь запрос AJAX из WebClient должны завершиться успешно. Методы GET, PUT и POST все разрешены.

![](enabling-cross-origin-requests-in-web-api/_static/image9.png)

<a id="how-it-works"></a>
## <a name="how-cors-works"></a>Как работает CORS

В этом разделе описывается, что происходит в запрос CORS на уровне сообщений HTTP. Очень важно понять, как работает CORS, таким образом, можно настроить **[EnableCors]** атрибут правильно и устранить неполадки, если не все работает как надо.

Спецификация CORS представляет ряд новых заголовков HTTP, которые позволяют запросы независимо от источника. Если браузер поддерживает CORS, он устанавливает эти заголовки автоматически для запросов о происхождении; не нужно предпринимать специальные действия в коде JavaScript.

Вот пример запроса независимо от источника. Заголовка «Origin» предоставляет домену узла, который выполняет запрос.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

Если сервер разрешает запрос, он задает заголовка Access-Control-Allow-Origin. Значение этого заголовка соответствует заголовку источника либо является использование подстановочного знака "\*«, это значит, что допускается любого источника.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

Если ответ не содержит заголовка Access-Control-Allow-Origin, сбоя запроса AJAX. В частности браузер запрещает запрос. Даже если сервер возвращает успешный ответ, браузер не предоставить ответ клиентскому приложению.

**Предварительные запросы**

Для некоторых запросов CORS браузер посылает дополнительный запрос, называется «Предварительный запрос,» перед отправкой самого запроса для ресурса.

Браузер можно пропустить Предварительный запрос, если выполняются следующие условия:

- Метод запроса — GET, HEAD или POST, *и*
- Приложение не устанавливает все заголовки запроса, отличные от Accept, Accept-Language, Content-Language, Content-Type или последнего-событие-ID, *и*
- Заголовок Content-Type (если задать) является одним из следующих: 

    - application/x-www-form-urlencoded
    - данные multipart/формы
    - text/plain

Правило о заголовках запроса применяется к заголовки, которые приложение задает путем вызова **setRequestHeader** на **XMLHttpRequest** объекта. (Спецификации CORS вызывает эти «заголовки запроса автора»). Правило не применяется к заголовкам *браузера* можно задать, например User-Agent, узла или Content-Length.

Ниже приведен пример Предварительный запрос:

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

Возможность предварительного запроса используется метод HTTP OPTIONS. Он включает два специальных заголовков:

- Access-Control-Request-Method: Метод HTTP, будет использоваться для самого запроса.
- Access-Control-Request-Headers: Список заголовков запросов, *приложения* задать для самого запроса. (Опять же, это не включает заголовки, которые задает браузера.)

Ниже приведен пример ответа, при условии, что сервер разрешает запрос.

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

Ответ содержит заголовок Access-Control-Allow-Methods, в которой перечислены разрешенные методы и при необходимости заголовок Access-Control-разрешить-Headers, в которой перечислены разрешенные заголовки. Если Предварительный запрос завершается успешно, браузер отправляет фактический запрос, как описано выше.

<a id="scope"></a>
## <a name="scope-rules-for-enablecors"></a>Правила области видимости для [EnableCors]

Вы можете включить CORS каждого действия, отдельного контроллера или глобально для всех контроллеров веб-API в приложении.

**Каждого действия**

Чтобы включить CORS для одного действия, задайте **[EnableCors]** атрибут в методе действия. В следующем примере включается CORS для `GetItem` только метод.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

**Для контроллера**

Если задать **[EnableCors]** класса контроллера, он применяется ко всем действиям в контроллере. Чтобы отключить CORS для действия, добавьте **[DisableCors]** атрибут к действию. В следующем примере включается CORS для каждого метода, за исключением `PutItem`.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

**Глобально**

Чтобы включить CORS для всех контроллеров веб-API в приложении, передайте **EnableCorsAttribute** экземпляр **EnableCors** метод:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

Если задать атрибут на более чем одну область, является порядок приоритета:

1. Действие
2. Контроллер
3. Global

<a id="allowed-origins"></a>
## <a name="set-the-allowed-origins"></a>Задайте разрешенные источники

*Источников* параметр **[EnableCors]** атрибут указывает, какие источники разрешены для доступа к ресурсу. Значение — разделенный запятыми список разрешенных источников.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

Можно также использовать подстановочное значение "\*" разрешать запросы от любого источников.

Тщательно обдумайте прежде чем разрешить запросы из любого источника. Это означает, что буквально любой веб-сайт может выполнять вызовы AJAX в веб-API.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

<a id="allowed-methods"></a>
## <a name="set-the-allowed-http-methods"></a>Задайте допустимый HTTP-методы

*Методы* параметр **[EnableCors]** атрибут указывает, какие методы HTTP получают доступ к ресурсу. Чтобы разрешить все методы, используйте подстановочное значение "\*«. Следующий пример разрешает только запросы GET и POST.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

<a id="allowed-request-headers"></a>
## <a name="set-the-allowed-request-headers"></a>Задать заголовки запросов

Ранее я описывал, как Предварительный запрос может включать заголовок Access-Control-Request-Headers, список заголовков HTTP, установленный приложением (так называемого «author заголовки запроса»). *Заголовки* параметр **[EnableCors]** атрибут указывает, какие заголовки запроса автор разрешены. Чтобы разрешить любые заголовки, задайте *заголовки* для "\*«. Чтобы добавить в список разрешений определенные заголовки, задайте *заголовки* в список с разделителями запятыми в разрешенные заголовки:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

Однако браузеры не согласованы полностью в установке Access-Control-Request-Headers. Например в настоящее время включает Chrome «началом координат»; Хотя FireFox не включают в себя стандартные заголовки, такие как «Принять», даже в том случае, когда приложение устанавливает их в скрипте.

Если задать *заголовки* на что-либо, отличное от «\*», следует включать по крайней мере «принять,» «content-type» и «началом координат», а также любые пользовательские заголовки, которые требуется поддерживать.

<a id="allowed-response-headers"></a>
## <a name="set-the-allowed-response-headers"></a>Набор допустимых заголовков

По умолчанию браузер не предоставляет все заголовки ответа для приложения. Заголовки ответа, которые доступны по умолчанию являются:

- Cache-Control
- Content-Language
- Content-Type
- Срок действия истекает
- Дата последнего изменения
- Директивы pragma

Спецификация CORS вызывает эти [заголовки ответа на простой](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header). Чтобы сделать доступными для приложения другие заголовки, установите *exposedHeaders* параметр **[EnableCors]**.

В следующем примере, контроллер `Get` метод задает настраиваемый заголовок с именем «X-Custom-Header». По умолчанию браузер не будет предоставлять этот заголовок в запрос независимо от источника. Чтобы сделать доступным заголовок, включите «X-Custom-Header» в *exposedHeaders*.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

<a id="credentials"></a>
## <a name="passing-credentials-in-cross-origin-requests"></a>Передача учетных данных в запросов о происхождении

Учетные данные, требующие особых действий в запрос CORS. По умолчанию браузер не отправляет никаких учетных данных с помощью запроса независимо от источника. Учетные данные содержат файлы cookie, а также схемы проверки подлинности HTTP. Для отправки учетных данных с помощью запроса независимо от источника, клиент должен указать **XMLHttpRequest.withCredentials** значение true.

С помощью **XMLHttpRequest** напрямую:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

В jQuery:

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

Кроме того сервер необходимо разрешить учетные данные. Чтобы разрешить учетные данные от источника в веб-API, задайте **SupportsCredentials** присвоено значение true, если **[EnableCors]** атрибут:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

Если это свойство имеет значение true, HTTP-ответа будет включать заголовок доступа-элемент управления-Allow-Credentials. Этот заголовок указывает обозревателю, учетные данные для запроса независимо от источника, поддерживает ли сервер.

Если браузер отправляет учетные данные, но ответ не включает допустимый заголовка Access-элемент управления-Allow-Credentials, браузер не будет предоставлять доступ приложению ответ и происходит сбой запроса AJAX.

Должны быть очень осторожными параметр **SupportsCredentials** значение true, так как это означает, что веб-сайт в другом домене может передать учетные данные вошедшего в систему пользователя веб-API от имени пользователя без оповещения пользователя. Спецификации CORS также гласит, что параметр *источников* для &quot; \* &quot; является недопустимым при **SupportsCredentials** имеет значение true.

<a id="cors-policy-providers"></a>
## <a name="custom-cors-policy-providers"></a>Поставщики пользовательских CORS политики

**[EnableCors]** атрибут реализует **ICorsPolicyProvider** интерфейс. Можно предоставить собственную реализацию, создав класс, производный от **атрибут** и реализует **ICorsProlicyProvider**.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

Теперь можно применить атрибут, в любом месте, что можно поместить **[EnableCors]**.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

Например пользовательский поставщик политики CORS удалось считать параметры из файла конфигурации.

В качестве альтернативы с помощью атрибутов, вы можете зарегистрировать **ICorsPolicyProviderFactory** объект, который создает **ICorsPolicyProvider** объектов.

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

Чтобы задать **ICorsPolicyProviderFactory**, вызовите **SetCorsPolicyProviderFactory** метод расширения во время запуска, как показано ниже:

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

<a id="browser-support"></a>
## <a name="browser-support"></a>Поддержка браузеров

Пакет CORS веб-API — это технология на стороне сервера. Браузер пользователя также должен поддерживать CORS. К счастью, в текущих версиях все основные браузеры включают [поддержка CORS](http://caniuse.com/cors).

Internet Explorer 8 и Internet Explorer 9, имеют частичная поддержка CORS, вместо устаревший объект XDomainRequest XMLHttpRequest. Дополнительные сведения см. в разделе [XDomainRequest - ограничения, ограничения и обходные пути](https://blogs.msdn.com/b/ieinternals/archive/2010/05/13/xdomainrequest-restrictions-limitations-and-workarounds.aspx).
