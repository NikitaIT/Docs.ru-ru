---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/design-to-survive-failures
title: Проектирование для обработки отказов (Создание реальных облачных приложений в Azure) | Документация Майкрософт
author: MikeWasson
description: Создание реальных облачных приложений в условиях электронная книга основана на презентацию, разработанная Скоттом Гатри. Здесь объясняется, 13 шаблонов и практических рекомендаций, которые он может...
ms.author: riande
ms.date: 06/12/2014
ms.assetid: 364ce84e-5af8-4e08-afc9-75a512b01f84
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/design-to-survive-failures
msc.type: authoredcontent
ms.openlocfilehash: 1098892c621b8add624b18788076be2fac1cc158
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41828196"
---
<a name="design-to-survive-failures-building-real-world-cloud-apps-with-azure"></a>Проектирование для обработки отказов (Создание реальных облачных приложений в Azure)
====================
по [Майк Уоссон](https://github.com/MikeWasson), [Рик Андерсон](https://github.com/Rick-Anderson), [том Дайкстра](https://github.com/tdykstra)

[Загрузить решение проект](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4) или [скачайте электронную книгу](http://blogs.msdn.com/b/microsoft_press/archive/2014/07/23/free-ebook-building-cloud-apps-with-microsoft-azure.aspx)

> **Создание реальных облачных приложений в условиях Azure** электронная книга основана на презентацию, разработанная Скоттом Гатри. Здесь объясняется 13 шаблоны и рекомендации, которые помогут вам быть в успешном развертывании веб-приложений для облака. Сведения о книге, см. в разделе [первой главы](introduction.md).


Среди прочего, вам нужно подумать о при построении любого типа приложения, но особенно один, который будет выполняться в облаке, где большое число пользователей будет использовать, — это том, как разработать приложение таким образом, чтобы он корректно обрабатывать сбои и продолжали выполнять доставку значение настолько, насколько возможно. Имея достаточно времени, действия будут пойти не так в любой среде или в любой программной системы. Как приложение обрабатывает ситуации определяет как получит клиентов и сколько времени на обработку, анализа и устранения проблем.

## <a name="types-of-failures"></a>Типы сбоев

Существует два основных типа сбоев, которые необходимо обрабатывать по-разному:

- Несохраняемым, самостоятельное восстановление, сбои, такие как временные сетевые неполадки.
- Долгосрочными сбоев, которые требуют вмешательства.

Для обработки временных ошибок можно реализовать политику повтора, чтобы убедиться, что в большинстве случаев приложение восстанавливает быстро и автоматически. Пользователи могут заметят, что немного больше времени ответа, но в противном случае они не будут затронуты. Мы покажем некоторые способы обработки этих ошибок в [обработка временного сбоя глава](transient-fault-handling.md).

Для долгосрочными сбоев, можно реализовать мониторинг и ведение журнала функциональные возможности, оповещает вас незамедлительно возникают проблемы, которая упрощает анализ основных причин. Мы покажем некоторые способы помогут справиться эти виды ошибок в [мониторинг и данные телеметрии глава](monitoring-and-telemetry.md).

## <a name="failure-scope"></a>Область сбоя

Также нужно думать об области сбоя — ли затрагивается одной машины, всей службы, такие как базы данных SQL, хранилища или всего региона.

![Область сбоя](design-to-survive-failures/_static/image1.png)

### <a name="machine-failures"></a>Аппаратные сбои

В Azure сервер автоматически заменяется на новый и хорошо спроектированной облачного приложения восстановления из подобного рода сбоев, автоматически и быстро. Ранее мы подчеркнуть преимущества масштабируемости без отслеживания состояния веб-уровня и простота восстановление после сбоя сервера является еще одним преимуществом отсутствие поддержки состояния. Простота восстановления также является одним из преимуществ возможностей платформы как службы (PaaS), таких как базы данных SQL и веб-приложениях службы приложений Azure. Сбои оборудования редки, но в случае, если эти службы обработать их автоматически. даже не нужно писать код для обработки сбоев компьютера, при использовании одной из этих служб.

### <a name="service-failures"></a>Сбои службы

Облачные приложения обычно используется несколько служб. Например приложение Fix It использует службу базы данных SQL, в службу хранилища, и веб-приложение развертывается в службе приложений Azure. Что будет приложения? при сбое одной из служб, от которых зависит Некоторые службы сбоев понятное «к сожалению, повторите попытку позже» сообщение может быть оптимальным, можно сделать. Но во многих случаях вы можем добиться лучшего результата. К примеру Если хранилище данных на сервере отключены, можно принимать входные данные пользователя, отображения «ваш запрос получен» и временно; хранить куда-нибудь еще входные данные Затем при службы, вам потребуется не вернется, вы сможете получить входные данные и обработать его.

[Рабочий шаблон на основе очередей](queue-centric-work-pattern.md) Глава показан один из способов обработки этого сценария. Приложение Fix It сохраняет задачи в базе данных SQL, но его не нужно выйти из программы работать, когда база данных SQL не работает. В этой главе узнаете, как для хранения входных данных пользователя для задачи в очереди и использовать рабочий процесс для чтения очереди и обновлении задачи. Если SQL не работает, возможность создания Fix It задачи не затрагиваются; рабочий процесс можно ожидать и обрабатывать новые задачи, когда база данных SQL доступна.

### <a name="region-failures"></a>Сбои региона

Целые регионы может завершиться ошибкой. Стихийного бедствия может уничтожить центра обработки данных, он может получить сведенные по meteor, строке магистрали в центре обработки данных может быть сократить фармер, burying компанию с экскаватора и т. д. Если приложение размещено в центре обработки данных stricken что делать? Это можно настроить приложение в Azure для запуска в нескольких регионах одновременно, таким образом, чтобы в случае аварии в одном, вы по-прежнему работает в другом регионе. Такие сбои происходят очень редко происходят, и большинство приложений не переходить по задаваясь, необходимо обеспечить непрерывное обслуживание через ошибки такого рода. См. в разделе "ресурсы" в конце главы, сведения о том, как обеспечить приложение стало доступно, даже из регионального сбоя.

Цель Azure — облегчают управление любых сбоев гораздо проще, и вы увидите некоторые примеры как мы делаем, в следующих главах.

## <a name="slas"></a>Соглашения об уровне обслуживания

Часто говорят о соглашение об уровне обслуживания (SLA) в облачной среде. По сути это предприятие обещания о том, как надежные их службы. На уровне 99,9%, соглашение об уровне ОБСЛУЖИВАНИЯ означает, что вы должны ожидать, что служба работает правильно 99,9% времени. Это довольно стандартное значение для соглашением об уровне ОБСЛУЖИВАНИЯ и мне кажется очень большое значение, но вы можете не знать, сколько времени вниз. 1% фактически равносильно. Ниже приведена таблица, показывающая количество времени простоя, различные процентные показатели соглашения об уровне ОБСЛУЖИВАНИЯ сумма за год, месяц и недели.

![Соглашение об уровне ОБСЛУЖИВАНИЯ таблицы](design-to-survive-failures/_static/image2.png)

Таким образом на уровне 99,9%, соглашение об уровне ОБСЛУЖИВАНИЯ означает, что служба может быть недоступен 8,76 часа в год или 43,2 мин в месяц. Это более время простоя, чем реализовать большинство людей. Таким образом, как разработчик вы хотите Имейте в виду, что возможна некоторое время простоя и обработать его удобным способом. В некоторый момент кто-то будет использоваться приложение, и служба будет неисправным свести к минимуму отрицательное воздействие этого на клиента.

Следует знать о SLA лишь какой промежуток времени, он ссылается на: часы сбрасываться еженедельно, ежемесячно или каждый год? В Azure мы сброса часов каждый месяц, который лучше для вас годового соглашения об уровне ОБСЛУЖИВАНИЯ, поскольку годового соглашения об уровне ОБСЛУЖИВАНИЯ может скрыть неправильный месяцев путем их смещения с нескольких месяцев хорошо.

Само собой мы всегда стремимся выполняться лучше, чем соглашение об уровне ОБСЛУЖИВАНИЯ; Обычно вы будете вниз значительно меньше, чем. Обещание является то, что если мы когда-либо работу дольше, чем максимальное время простоя можно запросить вернуть деньги. Сумму денег, вы получаете, вероятно, не компенсируют влияние данного избыточные время простоя, но этот аспект SLA действует как политика принудительного применения и позволит узнать, что мы его очень серьезно.

### <a name="composite-slas"></a>Составные соглашения об уровне обслуживания

Важно подумать о при просмотре в соглашениях об уровне обслуживания является влияние использования нескольких служб в приложении, с помощью каждая служба отдельные соглашения об уровне ОБСЛУЖИВАНИЯ. Например приложение Fix It использует веб-приложениях службы приложений Azure, службе хранилища Azure и базы данных SQL. Ниже приведены их номера соглашения об уровне ОБСЛУЖИВАНИЯ, по состоянию на дату в этой электронной книге записывается в декабря 2013 г.

![База данных SQL веб-сайта, хранилища, соглашения об уровне ОБСЛУЖИВАНИЯ](design-to-survive-failures/_static/image3.png)

Каково максимальное время, характерные для приложения, в зависимости от этих соглашений об уровне обслуживания? Вы можете подумать, что на время простоя будет равно худшее процент соглашение об уровне ОБСЛУЖИВАНИЯ или 99,9% в данном случае. Это было бы true, если все три службы всегда сбой в то же время, но не обязательно что произойдет. Каждая служба может завершиться ошибкой независимо друг от друга в разное время, поэтому вам нужно рассчитать составное SLA путем умножения отдельных номеров соглашения об уровне ОБСЛУЖИВАНИЯ.

![Составное SLA](design-to-survive-failures/_static/image4.png)

Поэтому ваше приложение может не работает не только 43,2 мин месяц, но 3 раза этот период, 108 минут в месяц — и по-прежнему быть в пределах соглашения об уровне ОБСЛУЖИВАНИЯ Azure.

Эта проблема не является уникальным в Azure. Фактически, мы обеспечиваем лучшее облако соглашения об уровне обслуживания облачных служб доступны, и вы получите аналогичные проблемы при использовании любого поставщика облачных служб. Это подчеркивает — очень важно подумать о том, как разработать приложению обрабатывать сбои неизбежны службы корректно, так как может произойти достаточно часто воздействовать на ваших заказчиков или пользователей.

### <a name="cloud-slas-compared-to-enterprise-down-time-experience"></a>Облако соглашения об уровне обслуживания, по сравнению с простоями возможности enterprise

Иногда говорят, «В моем приложении enterprise никогда не возникают эти проблемы.» Если вы зададите вопрос, сколько время простоя в месяц них фактически имеются, они обычно скажем, «, она происходит время от времени.» И если вы зададите вопрос как часто, они признать, «Иногда нам нужно архивировать или установить новый сервер или обновления программного обеспечения». Само собой, который подсчитывает как время простоя. Большинство корпоративных приложений пока не будут особенно важных не фактически работают дольше, чем количество времени, допускаемых наших соглашений об уровне обслуживания. Но когда сервера и инфраструктуры, и вы отвечаете, для него и управление им, вы обычно думают меньше angst о простоями. В облачной среде вы зависит от кого-то другого, и вы не знаете, что происходит, поэтому вы может получить более беспокоитесь о его.

Когда предприятия достигается больше процент времени, чем вы получаете из облака соглашение об уровне ОБСЛУЖИВАНИЯ, их решения, тратя намного больше денег на оборудовании. Облачная служба может сделать это, однако будет взимать плату многое другое для своих служб. Вместо этого воспользоваться экономичной службой и разработки программного обеспечения, таким образом, чтобы сбои неизбежны вызывает Минимальное нарушение работы с заказчиками. Задание разработчик приложения cloud не столько, чтобы избежать сбоев, как избежать катастрофы, а это сделать, уделяя основное внимание программного обеспечения, а не на оборудовании. В то время как корпоративных приложений стремятся максимально увеличить время безотказной работы свести к минимуму среднее время восстановления стремимся облачных приложений.

### <a name="not-all-cloud-services-have-slas"></a>Не все облачные службы используют соглашения об уровне обслуживания

Имейте в виду также не каждой облачной службы даже наличие соглашением об уровне ОБСЛУЖИВАНИЯ. Если приложение зависит от службы с никакой гарантии, время бесперебойной работы, может быть вниз гораздо больше времени, чем можно себе представить. Например если включить вход на сайт с помощью поставщиков социальных сетей, таких как Facebook или Twitter, проверьте у поставщика услуг, если имеется соглашение и, возможно, не является одним. Но если выходит из строя или не могут поддерживать большое число запросов, которые вы используете службу проверки подлинности, заказчикам блокируются вне вашего приложения. Может быть недоступен, дней или больше. Создатели одно новое приложение Ожидалась сотен миллионов файлы для загрузки и приняла зависимость от проверки подлинности Facebook — но не взаимодействовать с Facebook, прежде чем live и обнаруженные слишком поздно, возникла не соглашение об уровне ОБСЛУЖИВАНИЯ для этой службы.

### <a name="not-all-downtime-counts-toward-slas"></a>Не все засчитывается время простоя соглашения об уровне обслуживания

Некоторыми облачными службам может запретить службы намеренно в том случае, если ваше приложение чрезмерно использует их. Это называется *регулирования*. Если служба имеет соглашение об уровне ОБСЛУЖИВАНИЯ, должно быть указано, условия, при которых может регулироваться, и макет приложения следует избегать этих условий и соответствующим образом реагировать на регулирование, если это происходит. Например запросов к службе начинает завершаться неудачно при превышении определенного числа в секунду, необходимо убедиться, что автоматические повторы не происходят не так быстро, что они смогут нанести регулирования продолжить. У нас Дополнительные сведения о регулировании в [обработка временного сбоя глава](transient-fault-handling.md).

## <a name="summary"></a>Сводка

В этой главе пытается помочь вам понять, почему реального облачного приложения должно быть разработано для обработки отказов корректно. Начиная с [следующей главе](monitoring-and-telemetry.md), остальные шаблоны в этой серии более подробно о некоторых стратегиях, которые можно использовать для этого:

- Хорошо иметь [мониторинг и телеметрия](monitoring-and-telemetry.md), чтобы вы быстро выяснить сбоев, которые требуют вмешательства, и у вас есть достаточно данных для их устранения.
- [Обработка временных сбоев](transient-fault-handling.md) путем реализации логики повторных попыток intelligent таким образом, чтобы приложение автоматически восстанавливает ее можно и возвращается к [Размыкатель цепи](transient-fault-handling.md#circuitbreakers) логики, если не удается.
- Используйте [распределенное кэширование](distributed-caching.md) чтобы свести к минимуму проблемы пропускной способности, задержке и подключения с помощью доступа к базе данных.
- Реализуйте ослабить связь через [шаблон на основе очередей рабочих](queue-centric-work-pattern.md), так что Интерфейсная часть вашего приложения можно продолжить работу, если серверной части не работает.

## <a name="resources"></a>Ресурсы

Дополнительные сведения см. в следующих главах эту электронную книгу и следующие ресурсы.

Документация:

- [Отказоустойчивость: Руководство по гибкой облачной архитектуре](https://msdn.microsoft.com/library/windowsazure/jj853352.aspx). Технический документ, Марк Меркьюри, Ульрих Хоманн и Эндрю Таунхилл. Версия веб-страницу видео из цикла Безаварийность.
- [Советы и рекомендации по проектированию крупномасштабных служб в облачных службах Azure](https://msdn.microsoft.com/library/windowsazure/jj717232.aspx). Технический документ по (Mark simms) и Майкл Томасси.
- [Техническое руководство по непрерывности бизнеса Azure](https://msdn.microsoft.com/library/windowsazure/hh873027.aspx). Технический документ, Patrick Wickline и Джейсон рот.
- [Аварийное восстановление и высокий уровень доступности для приложений Azure](https://msdn.microsoft.com/library/windowsazure/dn251004.aspx). Технический документ, Michael McKeown, Хану Коммалапати и Джейсон рот.
- [Microsoft Patterns and Practices — руководство по Azure](https://msdn.microsoft.com/library/dn568099.aspx). Центр обработки данных с несколькими см. руководство по развертыванию шаблона размыкателя цепи.
- [Поддержка Azure — соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/).
- [Непрерывность бизнес-процессов в базе данных Azure SQL](https://msdn.microsoft.com/library/windowsazure/hh852669.aspx). Документация о возможности базы данных SQL высокой доступности и аварийного восстановления.
- [Высокий уровень доступности и аварийное восстановление для SQL Server в виртуальных машинах](https://msdn.microsoft.com/library/windowsazure/jj870962.aspx).

Видеоролики:

- [FailSafe: Создание масштабируемых, надежных облачных служб](https://channel9.msdn.com/Series/FailSafe). Девять веб-трансляций, Ульрих Хоманн, Марк Меркьюри и (Mark simms). Отображает высокоуровневых концепциях и архитектурные принципы очень доступен и интересным способом, истории, извлеченных из консультационной группой Microsoft клиентов (CAT) опыт работы с реальными клиентами. Эпизоды 1 до 8 Двигайтесь вперед в причинами разработки облачных приложений для обработки отказов. См. также исполнению обсуждение регулирования в эпизод 2, начиная с 49:57 обсуждение точек и режимов сбоя в эпизод 2, начиная с 56:05 и обсуждение размыкатели цепи в эпизод 3, начиная с 40:55.
- [Построение Big: Уроках, извлеченных из клиентов Azure — часть II](https://channel9.msdn.com/Events/Build/2012/3-030). (Mark simms) рассказывает о разработке для сбоя и все, что инструментирование. Как и серия Failsafe, но переход в практические подробности.

> [!div class="step-by-step"]
> [Назад](unstructured-blob-storage.md)
> [Вперед](monitoring-and-telemetry.md)
