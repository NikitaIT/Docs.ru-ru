---
uid: mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-cs
title: Создание пользовательских маршрутов (C#) | Документация Майкрософт
author: microsoft
description: Узнайте, как добавить пользовательские маршруты для приложения ASP.NET MVC. В этом руководстве вы узнаете, как изменить таблицы маршрутизации по умолчанию в файле Global.asax.
ms.author: riande
ms.date: 02/16/2009
ms.assetid: 3cd08f02-8763-490a-b625-2ac96a24b73f
msc.legacyurl: /mvc/overview/older-versions-1/controllers-and-routing/creating-custom-routes-cs
msc.type: authoredcontent
ms.openlocfilehash: 1d7a25f9d257320c252408ae251e2f9f620930d8
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41828348"
---
<a name="creating-custom-routes-c"></a>Создание пользовательских маршрутов (C#)
====================
по [Microsoft](https://github.com/microsoft)

> Узнайте, как добавить пользовательские маршруты для приложения ASP.NET MVC. В этом руководстве вы узнаете, как изменить таблицы маршрутизации по умолчанию в файле Global.asax.


В этом руководстве вы узнаете, как добавить настраиваемый маршрут в приложении ASP.NET MVC. Вы узнаете, как изменение таблицы маршрутизации по умолчанию в файле Global.asax с настраиваемый маршрут.

Для многих простых приложений ASP.NET MVC таблицы маршрутизации по умолчанию будет работать нормально. Тем не менее может выясниться, что специализированные требования к маршрутизации. В этом случае можно создать настраиваемый маршрут.

Допустим, что вы создаете приложение блога. Может потребоваться обрабатывать входящие запросы, которые выглядят следующим образом:

/ Archive/12-25-2009

Когда пользователь вводит этот запрос, необходимо вернуть в записи блога, соответствующий дате 12/25/2009. Чтобы обработать этот тип запроса, необходимо создать настраиваемый маршрут.

Файл Global.asax в листинге 1 содержит новый настраиваемый маршрут с именем блог, который обрабатывает запросы, которые выглядят как /Archive/*Дата записи*.

**В листинге 1 - Global.asax (с настраиваемый маршрут)**

[!code-csharp[Main](creating-custom-routes-cs/samples/sample1.cs)]

Важен порядок маршруты, добавленные в таблицу маршрутов. Наш новый настраиваемый маршрут блога добавляется перед существующего маршрута по умолчанию. Если вы изменен порядок, а затем маршрут по умолчанию всегда будет вызван вместо пользовательского маршрута.

Настраиваемый маршрут блог соответствует любой запрос, который начинается с/Archive /. Таким образом он удовлетворяет всем следующим URL-адресам:

- / Archive/12-25-2009

- / Archive/10-6-2004

- / Archive/apple

Настраиваемый маршрут сопоставляет входящий запрос к контроллеру с именем архива и вызывает действие Entry(). При вызове метода Entry() Дата записи передается как параметр с именем entryDate.

Можно использовать настраиваемый маршрут блога с помощью контроллера в листинге 2.

**В листинге 2 - ArchiveController.cs**

[!code-csharp[Main](creating-custom-routes-cs/samples/sample2.cs)]

Обратите внимание на то, что метод Entry() в листинге 2 принимает параметр типа DateTime. Платформа MVC может преобразовать эту дату записи из URL-адрес в значение даты и времени автоматически. Если параметр записи даты в URL-адресе невозможно преобразовать в значение DateTime, возникает ошибка (см. рис. 1).

**Рис. 1. Ошибка в результате преобразования параметра**


[![В диалоговом окне нового проекта](creating-custom-routes-cs/_static/image1.jpg)](creating-custom-routes-cs/_static/image1.png)

**Рис 01**: ошибка в результате преобразования параметра ([Просмотр полноразмерного изображения](creating-custom-routes-cs/_static/image2.png))


## <a name="summary"></a>Сводка

Цель этого учебника было продемонстрировать, как можно создать настраиваемый маршрут. Вы узнали, как добавить настраиваемый маршрут в таблицу маршрутов в файле Global.asax, который представляет записи в блогах. Мы рассмотрели способ сопоставления запросов для записи в блогах контроллер с именем ArchiveController и действие контроллера с именем Entry().

> [!div class="step-by-step"]
> [Назад](aspnet-mvc-controllers-overview-cs.md)
> [Вперед](creating-a-route-constraint-cs.md)
