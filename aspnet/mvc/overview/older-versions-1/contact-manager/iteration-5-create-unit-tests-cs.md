---
uid: mvc/overview/older-versions-1/contact-manager/iteration-5-create-unit-tests-cs
title: 'Итерация #5 – Создание модульных тестов (C#) | Документация Майкрософт'
author: microsoft
description: В пятой итерации мы вам наше приложение проще обслуживании и изменении путем добавления модульных тестов. Мы макетирование наших классов модели данных и создания модульных тестов для o...
ms.author: riande
ms.date: 02/20/2009
ms.assetid: 28ad8f80-b8a5-444e-b478-8b15a846060c
msc.legacyurl: /mvc/overview/older-versions-1/contact-manager/iteration-5-create-unit-tests-cs
msc.type: authoredcontent
ms.openlocfilehash: 68d0ae15db115685b3e1a44f8b3b5b7e33674a8b
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41835488"
---
<a name="iteration-5--create-unit-tests-c"></a>Итерация #5 – Создание модульных тестов (C#)
====================
по [Microsoft](https://github.com/microsoft)

[Скачать код](iteration-5-create-unit-tests-cs/_static/contactmanager_5_cs1.zip)

> В пятой итерации мы вам наше приложение проще обслуживании и изменении путем добавления модульных тестов. Мы макетирование наших классов модели данных и создания модульных тестов для контроллеров и логику проверки.


## <a name="building-a-contact-management-aspnet-mvc-application-c"></a>Создание приложения управления контактами ASP.NET MVC (C#)

В этой серии руководств мы создаем всего приложения управления контактами от начала до конца. Приложение диспетчера контактов позволяет хранить контактные данные - имена, номера телефонов и адреса электронной почты — список людей.

Мы создаем приложение через несколько итераций. С каждой итерацией мы постепенно улучшить приложение. Этот подход с несколькими итерации предназначена для того, чтобы можно было понять причину для каждого изменения.

- Итерация #1 - Создание приложения. В первой итерации мы создадим Contact Manager простейшим способом невозможно. Мы добавили поддержку для основных операций базы данных: создание, чтение, обновление и удаление (CRUD).

- #2 - итерация приложения красиво выглядеть. В этой итерации мы улучшить внешний вид приложения, изменив значение по умолчанию master страница представления ASP.NET MVC и каскадные таблицы стилей.

- Итерации #3 - Добавление проверки форм. В третьей итерации мы добавим проверки базовой форме. Мы запретить Отправка формы без завершения обязательные поля. Кроме того, мы проверяем, адреса электронной почты и номера телефонов.

- Итерация #4 - Создание слабых связей в приложении. В этой четвертой итерации мы воспользоваться преимуществами нескольких шаблонов дизайна программного обеспечения, чтобы упростить обслуживании и изменении приложения диспетчера контактов. Например мы выполнили рефакторинг наше приложение, чтобы использовать шаблон репозитория и шаблон внедрения зависимостей.

- Итерации #5 - Создание модульных тестов. В пятой итерации мы вам наше приложение проще обслуживании и изменении путем добавления модульных тестов. Мы макетирование наших классов модели данных и создания модульных тестов для контроллеров и логику проверки.

- Итерация #6 - использование управляемой тестами разработки. В этой шестой итерации мы добавляем новые функциональные возможности в наше приложение, сначала написание модульных тестов и написании кода в отношении модульные тесты. В этой итерации мы добавляем групп контактов.

- Итерации #7 - Добавление функций Ajax. В седьмой итерации мы повысить скорость реагирования и производительность приложения, добавляя поддержку Ajax.


## <a name="this-iteration"></a>Эта итерация

В предыдущих итерациях приложения диспетчера контактов мы оптимизировали обеспечивать более слабую связь компонентов приложения. Мы запятыми приложение в различных контроллера, службы и репозитория слои. Каждый уровень взаимодействует с уровня под ним через интерфейсы.

Мы оптимизировали приложение, чтобы сделать приложение проще обслуживать и изменить. Например если нам нужно использовать новые технологии доступа к данным, мы может просто изменить уровня репозитория не изменяя при этом контроллер или уровня службы. Сделав в диспетчере контактов слабо связанный, мы ve сделал приложение более устойчивым к изменить.

Но что произойдет, если нам нужно добавить новый компонент в приложение диспетчера контактов? Или, что происходит, когда мы исправления ошибки? Жаль, что, но хорошо проверенных суть написания кода является то, что каждый раз, когда touch кода, создать риск появления новых ошибок.

Например один день нормально, ваш руководитель вас могут попросить добавить новый компонент в диспетчере контактов. Она хочет, чтобы добавить поддержку для группы контактов. Она хочет, чтобы пользователи могли выполнять разбиение контакты по группам друзей, бизнес-требования, и т. д.

Чтобы реализовать эту новую функцию, необходимо изменить все три уровня приложения диспетчера контактов. Необходимо расширить функциональные возможности контроллеров, уровень служб и хранилище. Сразу же начать изменять код, вы рискуете нарушений функциональности, работали.

Рефакторинг наше приложение в отдельные слои, как мы делали в предыдущих итерациях, был хорошо. Было хорошо, так как она позволяет вносить изменения в всей слои без затрагивания остальной его части приложения. Тем не менее если вы хотите сделать проще в обслуживании и изменять код в пределах слоя, необходимо создать модульные тесты для кода.

Использовании модульного теста для тестирования отдельный блок кода. Эти блоки кода, имеют меньший размер, чем слои всего приложения. Как правило использовании модульный тест для проверки ли конкретного метода в коде ведет себя так, предполагается, что. Например вы создадите модульный тест для метода CreateContact(), предоставляемых классом ContactManagerService.

Модульные тесты на работу приложения, так же, как средство подстраховки. При каждом изменении кода в приложении, можно выполнить набор модульных тестов, чтобы проверить, прерывается ли изменение выполнение существующие функциональные возможности. Модульные тесты сделать безопасно изменять код. Модульные тесты весь код в приложении повысить сопротивляемость для изменения.

В этой итерации мы добавляем модульные тесты в наше приложение диспетчера контактов. Таким образом, в следующей итерации, можно добавить группы контактов для нашего приложения, не беспокоясь о нарушить имеющуюся функциональность.

> [!NOTE] 
> 
> Существуют различные платформы, включая NUnit и xUnit.net, MbUnit модульного тестирования. В этом руководстве мы используем модульного тестирования в состав Visual Studio. Тем не менее можно легко использовать один из этих альтернативных платформ.


## <a name="what-gets-tested"></a>Что возвращает протестированы

В идеальном мире весь код должен быть охвачен модульными тестами. В идеальном мире вы бы идеальное средство подстраховки. Будет возможность изменять любую строку кода в приложении и сразу же узнавать, выполнив модульные тесты, ли изменение привело к нарушению существующие функциональные возможности.

Тем не менее мы кое t реальном времени, в идеальном мире. На практике, при написании модульных тестов вы сосредоточиться на написании тестов для бизнес-логики (например, логика проверки). В частности вы *не* написание модульных тестов данных доступ к логику или логику представления.

Чтобы быть полезным, модульные тесты должны выполняться очень быстро. Можно легко накапливать сотни или даже тысяч модульных тестов для приложения. Если модульные тесты занять много времени для запуска, а затем, чтобы избежать их выполнения. Другими словами, долго выполняющиеся модульные тесты являются бесполезными для кодирования изо дня в день.

По этой причине вы обычно не следует создавать модульные тесты для кода, который взаимодействует с базой данных. Запуске сотен модульных тестов для активной базы данных бы слишком много времени. Вместо этого макетирование базы данных и написать код, который взаимодействует с базой данных макетов (мы обсудим макетирование базы данных ниже).

По этой же причине вы обычно не следует создавать модульные тесты для представлений. Чтобы протестировать представления, необходимо развернуть веб-сервера. Так как вращающийся веб-сервер является сравнительно медленным процессом, создание модульных тестов для представления не рекомендуется.

Если представление содержит сложная логика затем можно переместить логику в вспомогательные методы. Можно написать модульные тесты для вспомогательных методов, которые выполняются не задействовав веб-сервера.

> [!NOTE] 
> 
> Хотя написание тестов для логики доступа к данным или логики представления не является хорошей идеей, при написании модульных тестов, эти тесты может быть очень ценным при стандартных функциональных или интеграции тестов.


> [!NOTE] 
> 
> ASP.NET MVC — это обработчик представлений Web Forms. Хотя обработчик представлений Web Forms зависит от веб-сервера, могут быть другие обработчиков представлений.


## <a name="using-a-mock-object-framework"></a>С помощью платформы макетов объектов

При создании модульных тестов, почти всегда необходимо воспользоваться преимуществами платформы макетирование объектов. Framework макетирование объектов позволяет создавать макеты и заглушки для классов в приложении.

Например вы используете платформу макетирование объектов, для создания имитационную версию класса репозитория. Таким образом, можно использовать класс фиктивного репозитория вместо класса реальные репозитории в модульных тестах. С помощью макета репозитория позволяет избежать выполнения кода базы данных при выполнении модульного теста.

Visual Studio включает платформу макетирование объектов. Тем не менее имеются несколько инфраструктур макетирования объект коммерческих с открытым исходным кодом для платформы .NET framework:

1. MOQ - эта платформа доступна по лицензии BSD открытым исходным кодом. Вы можете скачать Moq из [ https://code.google.com/p/moq/ ](https://code.google.com/p/moq/).
2. Rhino Mocks — эта платформа доступна по лицензии BSD открытым исходным кодом. Вы можете скачать Rhino Mocks из [ http://ayende.com/projects/rhino-mocks.aspx ](http://ayende.com/projects/rhino-mocks.aspx).
3. Typemock Isolator - это коммерческих framework. Можно загрузить ознакомительную версию с [ http://www.typemock.com/ ](http://www.typemock.com/).

В этом руководстве, я решил использовать Moq. Тем не менее можно просто использовать Rhino Mocks, или Typemock Isolator, чтобы создать макет объектов для приложения диспетчера контактов.

Перед использованием Moq, необходимо выполнить следующие действия:

1. .
2. Прежде чем распаковать его загрузки, убедитесь в том, щелкните правой кнопкой мыши файл и нажмите кнопку **Unblock** (см. рис. 1).
3. Распакуйте загрузки.
4. Добавьте ссылку на сборку Moq, щелкнув правой кнопкой мыши папку ссылок в проекте ContactManager.Tests и выбрав **добавить ссылку**. На вкладке обзора перейдите к папке, куда было распаковано Moq и выберите сборку Moq.dll. Нажмите кнопку **ОК** кнопки.
5. После выполнения этих действий, в папке "ссылки" должно выглядеть как на рис. 2.


[![Разблокировка Moq](iteration-5-create-unit-tests-cs/_static/image1.jpg)](iteration-5-create-unit-tests-cs/_static/image1.png)

**Рис 01**: разблокирующих Moq ([Просмотр полноразмерного изображения](iteration-5-create-unit-tests-cs/_static/image2.png))


[![Ссылки после добавления Moq](iteration-5-create-unit-tests-cs/_static/image2.jpg)](iteration-5-create-unit-tests-cs/_static/image3.png)

**Рис. 02**: ссылки после добавления Moq ([Просмотр полноразмерного изображения](iteration-5-create-unit-tests-cs/_static/image4.png))


## <a name="creating-unit-tests-for-the-service-layer"></a>Создание модульных тестов для уровня службы

Позвольте s начните с создания набора модульных тестов для наших Contact Manager s уровень прикладного сервиса. Мы будем использовать эти тесты, чтобы проверить логику проверки.

Создайте новую папку с именем моделей в проекте ContactManager.Tests. Затем щелкните папку Models правой кнопкой мыши и выберите **Add, новый тест**. **Добавление нового теста** появляется диалоговое окно, показанное на рис. 3. Выберите **модульного теста** шаблона и назовите новый тест ContactManagerServiceTest.cs. Нажмите кнопку **ОК** кнопку, чтобы добавить в проект тестирования нового теста.

> [!NOTE] 
> 
> Как правило требуется структура папок проекта теста в соответствии со структурой папок проекта ASP.NET MVC. Например вы поместите тесты контроллера в папку Controllers, тесты модели в папке моделей и так далее.


[![Models\ContactManagerServiceTest.cs](iteration-5-create-unit-tests-cs/_static/image3.jpg)](iteration-5-create-unit-tests-cs/_static/image5.png)

**Рис 03**: Models\ContactManagerServiceTest.cs ([Просмотр полноразмерного изображения](iteration-5-create-unit-tests-cs/_static/image6.png))


Сначала нам нужно протестировать метод CreateContact(), предоставляемых классом ContactManagerService. Мы создадим следующие пять тестов:

- CreateContact() - тесты, CreateContact() возвращает значение true, если допустимый контакт передается в метод.
- CreateContactRequiredFirstName() - тесты, что сообщение об ошибке добавляется в состояние модели после контакт с отсутствует имя передается в метод CreateContact().
- CreateContactRequredLastName() - тесты, что сообщение об ошибке добавляется в состояние модели после контакт с отсутствует имя последнего передается в метод CreateContact().
- CreateContactInvalidPhone() - тесты, что сообщение об ошибке добавляется в состояние модели после контакт с недопустимыми номерами телефонов передается в метод CreateContact().
- CreateContactInvalidEmail() - тесты, что сообщение об ошибке добавляется в состояние модели после контакт с адресом недопустимый адрес электронной почты передается в метод CreateContact()...

Первый тест проверяет, что допустимый контакт не создает ошибку проверки. Остальные тесты проверьте все правила проверки.

В листинге 1 содержится код для этих тестов.

**В листинге 1 - Models\ContactManagerServiceTest.cs**

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample1.cs)]


Так как мы используем класс Contact в листинге 1, нам нужно добавить ссылку на Microsoft Entity Framework тестовый проект. Добавьте ссылку на сборку System.Data.Entity.


В листинге 1 содержит метод с именем Initialize(), помеченный атрибутом [TestInitialize]. Этот метод автоматически вызывается перед выполнением каждого из модульных тестов (это называется 5 раз непосредственно перед каждым из модульных тестов). Метод Initialize() создает макет репозитория со следующей строкой кода:

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample2.cs)]

Эта строка кода использует Moq платформу для создания макета репозитория с помощью интерфейса IContactManagerRepository. Вместо фактического EntityContactManagerRepository фиктивного репозитория используется для предотвращения доступа к базе данных при запуске каждого модульного теста. Макет репозитория реализует методы интерфейса IContactManagerRepository, но фактически ничего делать t Дон методы.

> [!NOTE] 
> 
> При использовании Moq framework, нет разницы между \_mockRepository и \_mockRepository.Object. Первое из них ссылается на макет&lt;IContactManagerRepository&gt; класс, содержащий методы для указания того, как будет вести себя фиктивного репозитория. Последнее относится к фактическое фиктивный репозиторий, который реализует интерфейс IContactManagerRepository.


При создании экземпляра класса ContactManagerService, фиктивного репозитория используется в метод Initialize(). Все отдельные модульные тесты используют данный экземпляр класса ContactManagerService.

В листинге 1 содержит пять методов, которые соответствуют каждому модульных тестов. Каждый из этих методов дополнен атрибутом [TestMethod]. При запуске модульных тестов, вызывается любой метод, который имеет этот атрибут. Другими словами любой метод, помеченный атрибутом [TestMethod] является модульным тестом.

Первый модульный тест, с именем CreateContact(), проверяет, что вызов CreateContact() возвращает значение true, если допустимый экземпляр на класс Contact передается в метод. Тест создает экземпляр класса контакта, вызывает метод CreateContact() и проверяет, что CreateContact() возвращает значение true.

Остальные тесты убедитесь, что при вызове метода CreateContact() контакте недопустимый затем метод возвращает false и сообщение об ошибке ожидаемый проверки добавляется состояние модели. Например тест CreateContactRequiredFirstName() создает экземпляр класса, обратитесь в службу с пустой строкой для его свойства FirstName. Далее CreateContact() был вызван с недопустимым контактом. Наконец можно проверить, что CreateContact() возвращает false, и что состояния модели содержит сообщение об ошибке проверки ожидаемого «требуется имя.»

Можно выполнять модульные тесты в листинге 1, выбрав параметр меню **теста выполните все тесты в решении (CTRL + R, A)**. Результаты тестов отображаются в окне результатов теста (см. рис. 4).


[![Результаты теста](iteration-5-create-unit-tests-cs/_static/image4.jpg)](iteration-5-create-unit-tests-cs/_static/image7.png)

**Рис. 04**: результаты теста ([Просмотр полноразмерного изображения](iteration-5-create-unit-tests-cs/_static/image8.png))


## <a name="creating-unit-tests-for-controllers"></a>Создание модульных тестов для контроллеров

Приложение ASP.NETMVC управления потоком взаимодействия с пользователем. При тестировании контроллера, необходимо проверить ли контроллер возвращает правой действие результат и представления данных. Вы также можете проверить ли контроллер взаимодействует с классами модели должным образом.

Например листинге 2 содержит два модульных теста для контроллера контактов Create() метод. Первый модульный тест проверяет, что при Create() методу передается допустимый контакт, а затем метод Create() перенаправляет действие индекса. Другими словами при передаче допустимый контакт, Create() метод должен вернуть RedirectToRouteResult, который представляет действие индекса.

Мы кое t необходимо проверить уровень служб ContactManager, когда мы тестируем на уровне контроллера. Таким образом мы макетирование слой служб в методе Initialize, следующим кодом:

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample3.cs)]

В модульном тесте CreateValidContact() мы выполняющие имитирование поведения вызова уровня службы CreateContact() метод со следующей строкой кода:

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample4.cs)]

Эта строка кода, приводит к макетов служба ContactManager возвращают значение true, когда вызывается его метод CreateContact(). Путем имитации слой служб, можно проверить поведение нашего контроллера без необходимости выполнения любого кода на уровне служб.

Второй модульный тест проверяет, что действие Create() возвращает представления создания, если методу передается недопустимый контакт. Мы вызывают слой служб CreateContact() метод вернет значение false с помощью следующей строки кода:

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample5.cs)]

Если метод Create() ведет себя так, как ожидается, что затем она возвращает представления создания при слой служб возвращает значение false. Таким образом, контроллер может отобразить сообщений об ошибках проверки в представления создания, и пользователь имеет возможность устранить, недопустимые свойства контакта.


Если вы планируете создать модульные тесты для контроллеров, необходимо вернуть имена явное представление из действий контроллера. Например не дают представления следующим образом:

Return View();

Вместо этого можно возвращайте представление следующим образом:

вернуть View("Create");

При отсутствии явной при возврате представления ViewResult.ViewName свойство возвращает пустую строку.


**В листинге 2 - Controllers\ContactControllerTest.cs**

[!code-csharp[Main](iteration-5-create-unit-tests-cs/samples/sample6.cs)]

## <a name="summary"></a>Сводка

В этой итерации мы создали модульных тестов для нашего приложения диспетчера контактов. Мы можно выполнять эти модульные тесты в любое время, чтобы убедиться, что наше приложение по-прежнему ведет себя так, как ожидается. Модульные тесты действовать как средство подстраховки для нашего приложения, благодаря чему мы можем безопасно изменить наше приложение в будущем.

Мы создали два набора модульных тестов. Во-первых мы протестировали наша логика проверки путем создания модульных тестов для наш слой служб. Далее мы протестировали логику потока управления путем создания модульных тестов для наших слоя контроллера. При тестировании наш слой служб, мы изолируются наши тесты наших на уровне службы из наших уровня репозитория макетирование наших уровня репозитория. При тестировании на уровне контроллера, мы изолированное наши тесты для наших слоя контроллера по макетирование уровня службы.

В следующей итерации мы изменим приложение диспетчера контактов, так, чтобы она поддерживала группы контактов. Мы добавим эти новые функции в наше приложение, используя процесс разработки программного обеспечения с именем управляемой тестами разработки.

> [!div class="step-by-step"]
> [Назад](iteration-4-make-the-application-loosely-coupled-cs.md)
> [Вперед](iteration-6-use-test-driven-development-cs.md)
