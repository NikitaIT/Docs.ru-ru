---
uid: web-pages/overview/ui-layouts-and-themes/9-working-with-images
title: Работа с изображениями на сайте ASP.NET Web Pages (Razor) | Документация Майкрософт
author: tfitzmac
description: В этой главе показано, как добавить, отображения и управления ими (изменение размера, отражение и добавление водяных знаков) в веб-сайта.
ms.author: riande
ms.date: 02/20/2014
ms.assetid: 778c4e58-4372-4d25-bab9-aec4a8d8e38d
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/9-working-with-images
msc.type: authoredcontent
ms.openlocfilehash: e609cd1c6ab74b5b40d28bde353501dbacb5d544
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41838508"
---
<a name="working-with-images-in-an-aspnet-web-pages-razor-site"></a>Работа с изображениями на сайте ASP.NET Web Pages (Razor)
====================
по [Tom FitzMacken](https://github.com/tfitzmac)

> В этой статье показано, как добавить, отображения и управления ими (изменение размера, отражение и добавление водяных знаков) в на веб-сайте ASP.NET Web Pages (Razor).
> 
> Вы узнаете, как:
> 
> - Как добавить изображение к странице динамически.
> - Как разрешить пользователям отправлять изображения.
> - Как изменить размер изображения.
> - Как отражение и поворот изображения.
> - Инструкции по добавлению к изображению водяного знака.
> - Сведения об использовании изображения в качестве водяного знака.
> 
> Ниже перечислены возможности, представленные в этой статье программирования ASP.NET:
> 
> - `WebImage` Вспомогательный.
> - `Path` Объект, который предоставляет методы, позволяющие управлять путь и имена файлов.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Версии программного обеспечения, используемые в этом руководстве
> 
> 
> - Веб-страниц ASP.NET (Razor) 2
> - WebMatrix 2
>   
> 
> Этот учебник также работает с WebMatrix 3.


<a id="Adding_an_Image"></a>
## <a name="adding-an-image-to-a-web-page-dynamically"></a>Динамическое добавление изображения на веб-страницу

Образы можно добавить на веб-сайт и на отдельные страницы во время разработки веб-сайта. Можно также разрешить пользователям отправлять изображения, которые могут пригодиться для задач, например сюда и добавить фотографию профиля.

Если образ уже доступен на веб-узла и вы хотите отобразить ее на странице, можно использовать HTML `<img>` следующего вида:

[!code-html[Main](9-working-with-images/samples/sample1.html)]

Иногда, однако необходимо иметь возможность динамически отображать изображения &#8212; то есть вы не знаете, что изображение для отображения, пока страница выполняется.

Процедуру, описанную в этом разделе показано, как отобразить изображение в режиме реального времени, где пользователям указать имя файла изображения из списка имен образов. Они выбирают имя образа из раскрывающегося списка, и при отправке страницы отображается изображение, которое они выбраны.

![[image]](9-working-with-images/_static/image1.jpg "ch9images 1.jpg")

1. В WebMatrix создайте новый веб-сайт.
2. Добавьте новую страницу с именем *DynamicImage.cshtml*.
3. В корневой папке веб-сайта, добавьте новую папку и назовите его *образы*.
4. Добавьте четыре образа для *образы* созданную папку. (Все образы у вас под рукой будет сделать, но они должна помещаться на страницу). Переименуйте изображения *Photo1.jpg*, *Photo2.jpg*, *Photo3.jpg*, и *Photo4.jpg*. (Вы не планируете использовать *Photo4.jpg* в этой процедуре, но оно будет использоваться далее в этой статье.)
5. Убедитесь, что четыре образа не помечены как доступные только для чтения.
6. Замените существующее содержимое на странице следующее:

    [!code-cshtml[Main](9-working-with-images/samples/sample2.cshtml)]

    Тело страницы имеет стрелку раскрывающегося списка ( `<select>` элемент) с именем `photoChoice`. Список не содержит три варианта и `value` атрибут каждого варианта в списке имеет имя одного из образов, помещенные в *образы* папки. По сути, позволяет пользователю выбрать понятное имя, например в списке &quot;Photo 1&quot;, а затем передает *.jpg* имя файла при отправке страницы.

    В коде, можно получить выбора пользователя (другими словами, имя файла образа) в списке, считывая `Request["photoChoice"]`. Сначала отобразится, если имеется выделение вообще. Если есть, можно составить путь для изображения, которое состоит из имени папки для образов и имя файла изображения пользователя. (Если вы попытались составить путь, но ничего не в `Request["photoChoice"]`, отобразится сообщение об ошибке.) Это приводит к относительный путь следующим образом:

    *образы/Photo1.jpg*

    Путь хранится в переменной с именем `imagePath` , которые потребуются позднее на странице.

    В тексте, имеется также `<img>` элемент, используемый для отображения изображения, выбранного пользователем. `src` Атрибута не задано имя файла или URL-адрес, как для отображения статического элемента. Вместо этого он становится равным `@imagePath`, это означает, что он получает свое значение из пути, заданных в коде.

    При первом запуске страницы, но нет нет изображения, отображаемого, так как пользователь еще не выбрано ничего. Это обычно означает, что `src` атрибута будут пустыми, и изображение будет отображаться красный &quot;x&quot; (или все, что браузер отображает, когда не удается найти образ). Чтобы избежать этого, поместите `<img>` элемент в `if` блок, который проверяет ли `imagePath` переменной не имеет ничего общего в нем. Если пользователь сделал выбор, `imagePath` содержит путь. Если пользователь не выбирает изображение или страница отображается, если это первый случай, `<img>` даже при отображении элемента.
7. Сохраните файл и откройте страницу в браузере. (Убедитесь, что выбран страницы **файлы** рабочей области, прежде чем запускать его.)
8. Выберите изображение в раскрывающемся списке и нажмите кнопку **пример изображения**. Убедитесь, что вы видите различных изображений для различных вариантах.

<a id="Uploading_an_Image"></a>
## <a name="uploading-an-image"></a>Отправка образа

В предыдущем примере показан способ отображения изображения динамически, но он работал только с образами, которые уже были на веб-сайте. Эта процедура показано, как разрешить пользователям загружать изображение, которое отображается на странице. В ASP.NET, можно управлять образы на основе `WebImage` вспомогательного приложения, который содержит методы, позволяющие создавать, управлять и сохранение изображений. `WebImage` Вспомогательный поддерживает все распространенные web типы файлов изображений, включая *.jpg*, *.png*, и *.bmp*. В этой статье вы будете использовать *.jpg* изображения, но можно использовать любой из типов изображений.

![[image]](9-working-with-images/_static/image2.jpg "ch9images 2.jpg")

1. Добавьте новую страницу и назовите его *UploadImage.cshtml*.
2. Замените существующее содержимое на странице следующее: 

    [!code-cshtml[Main](9-working-with-images/samples/sample3.cshtml)]

    В теле текста `<input type="file">` элемент, который позволяет пользователям выбрать файл для передачи. Когда пользователи выбирают **отправить**, их файл отправляется вместе с формы.

    Чтобы получить отправленный образ, используйте `WebImage` вспомогательного приложения, которая имеет все виды полезных методов для работы с изображениями. В частности, использовать `WebImage.GetImageFromRequest` получить отправленный образ (при наличии) и сохраните его в переменной с именем `photo`.

    Массу работы в этом примере включает в себя получение и установка файлов и путей. Проблема заключается в необходимость получения имени (и имени), изображения, которое пользователь отправил, а затем создайте новый путь, для которой вы собираетесь сохранить образ. Поскольку пользователи потенциально может передать несколько образов, тем же именем, используется немного дополнительного кода для создания уникальных имен и убедитесь, что пользователи не перезаписывайте существующие фото.

    Если изображение фактически будут переданы (тест `if (photo != null)`), вы получите имя образа из образа `FileName` свойство. Когда пользователь отправляет изображение, `FileName` содержит исходное имя пользователя, которое включает в себя путь с компьютера пользователя. Он может выглядеть следующим образом:

    *C:\Users\Joe\Pictures\SamplePhoto1.jpg*

    Вы не хотите пути данных, однако &#8212; необходимо просто фактическим именем файла (*SamplePhoto1.jpg*). Только файл из пути можно убрать с помощью `Path.GetFileName` метод следующим образом:

    [!code-csharp[Main](9-working-with-images/samples/sample4.cs)]

    Создайте новый уникальное имя файла, добавив GUID исходного имени. (Дополнительные сведения об идентификаторах GUID см. в разделе [о GUID](#SB_AboutGUIDs) далее в этой статье.) Затем можно создать полный путь, который можно использовать для сохранения изображения. Сохранить путь состоит из имени файла, папке (образов) и текущее расположение веб-сайта.

    > [!NOTE]
    > Чтобы код, чтобы сохранять файлы в *образы* папки, приложение должно чтения и записи разрешения для этой папки. На компьютере разработчика не обычно это проблема. Тем не менее при публикации сайта поставщика услуг размещения веб-сервер, может потребоваться явно задать эти разрешения. Если вы запустите этот код на сервере поставщика услуг размещения и ошибки, проверьте с помощью поставщика услуг размещения, чтобы узнать, как задать эти разрешения.

    Наконец, передайте сохранения путь к `Save` метод `WebImage` вспомогательный. Это сохраняет отправленный образ с новым именем. Сохранить метод выглядит следующим образом: `photo.Save(@"~\" + imagePath)`. Полный путь добавляется к `@"~\"`, — текущее расположение веб-сайта. (Сведения о `~` оператора, см. в разделе [введение в ASP.NET веб-программирование с помощью синтаксиса Razor](https://go.microsoft.com/fwlink/?LinkId=202890#ID_WorkingWithFileAndFolderPaths).)

    Как показано в предыдущем примере содержит основной области страницы `<img>` элемент для отображения изображения. Если `imagePath` задана `<img>` элемент подготавливается к просмотру и его `src` атрибуту присваивается `imagePath` значение.
3. Откройте страницу в браузере.
4. Передача образа и убедитесь, что он отображается на странице.
5. Откройте в веб-узла, *образы* папки. Вы увидите, что был добавлен новый файл, имя которого выглядит примерно следующим образом: 

    *45ea4527-7ddd-4965-b9ca-c6444982b342\_MyPhoto.png*

    Это образ, загруженный с идентификатором GUID, в качестве префикса имени. (Свой собственный файл будет иметь другой GUID и, возможно, имеет имя, отличные от *MyPhoto.png*.)

> [!TIP] 
> 
> <a id="SB_AboutGUIDs"></a>
> ### <a name="about-guids"></a>Об идентификаторах GUID
> 
> Идентификатор GUID (глобальный уникальный идентификатор) является идентификатором, который обычно отображается в виде следующим образом: `936DA01F-9ABD-4d9d-80C7-02AF85C822A8`. Цифры и буквы (от A до F) отличаются для каждого идентификатора GUID, но все они соответствуют шаблону с помощью групп 8-4-4-4-12 символов. (С технической точки зрения GUID представляет 16-байтовый/128-разрядное число). Если вам требуется идентификатор GUID, можно вызвать специализированный код, который создает идентификатор GUID для вас. Идея идентификаторы GUID в том, что между огромного размера, числа (3,4 x 10<sup>38</sup>) и алгоритм создания его, полученного числа гарантированно быть одного типа. Идентификаторы GUID, таким образом, являются хорошим способом для создания имен для таких вещей, в том случае, когда необходимо гарантировать, что вы не использовали дважды тем же именем. Недостаток, само собой, заключается в том, что идентификаторы GUID не особенно понятное для пользователя, поэтому они обычно используются, когда имя используется только в коде.


<a id="Resizing_an_Image"></a>
## <a name="resizing-an-image"></a>Изменение размера изображения

Если веб-сайт принимает образов от пользователя, может потребоваться изменить размер изображения, прежде чем отображать или сохранить их. Можно повторно использовать `WebImage` вспомогательного метода для этого.

Эта процедура описывается изменение размера отправленного изображения, создание эскизов, а затем сохраните эскиз и исходное изображение на веб-сайте. На странице отображаются эскиз и использовать гиперссылки для перенаправления пользователей полноразмерное изображение.

![[image]](9-working-with-images/_static/image3.jpg "ch9images 3.jpg")

1. Добавьте новую страницу с именем *Thumbnail.cshtml*.
2. В *образы* папке создайте вложенную папку с именем *thumbs*.
3. Замените существующее содержимое на странице следующее: 

    [!code-cshtml[Main](9-working-with-images/samples/sample5.cshtml)]

    Этот код аналогичен коду из предыдущего примера. Разница заключается в том, что этот код сохраняет изображение дважды, один раз в обычном режиме и один раз после создания эскиза копию образа. Сначала получить отправленный образ и сохраните его в *образы* папки. Вы затем создать новый путь для эскиза. Для создания эскиза, вызовите `WebImage` вспомогательного приложения `Resize` метод, чтобы создать образ 60 пикселей на 60 пикселей. В примере показано, как сохранение пропорций и как можно предотвратить изображение увеличена (если новый размер фактически сделает образ большего размера). Изображение с измененным размером затем сохраняется в *thumbs* во вложенную папку.

    В конце разметку, используется тот же `<img>` элемент с динамической `src` атрибут, который вы видели в предыдущих примерах поле для отображения изображения. В этом случае можно отобразить эскиз. Можно также использовать `<a>` элемент, чтобы создать гиперссылку на больших версию образа. Как и в `src` атрибут `<img>` задать элемент, `href` атрибут `<a>` элемент динамически, чтобы содержимое `imagePath`. Чтобы убедиться в том, что путь может работать как URL-адрес, следует передать `imagePath` для `Html.AttributeEncode` метод, который преобразует зарезервированные символы в пути символы, которые допустимы в URL-адрес.
4. Откройте страницу в браузере.
5. Отправить фотографию и убедитесь, что отображается эскиз.
6. Щелкните эскиз, чтобы увеличить изображение.
7. В *образы* и *изображений и эскизов*, обратите внимание на то, что были добавлены новые файлы.

<a id="Rotating_and_Flipping"></a>
## <a name="rotating-and-flipping-an-image"></a>Вращение и отражение изображения

`WebImage` Вспомогательный также позволяет отразить и поворота изображения. Эта процедура показано, как получить изображение с сервера, отражение изображения сверху вниз (по вертикали), сохраните его и затем отобразить отраженное изображение на странице. В этом примере вы используете файл, у вас уже есть на сервере (*Photo2.jpg*). В реальном приложении вероятно, будет перевернуть изображение, имя которого вы получаете динамически, как это было сделано в предыдущих примерах.

![[image]](9-working-with-images/_static/image4.jpg "ch9images 4.jpg")

1. Добавьте новую страницу с именем *FlipImage.cshtml*.
2. Замените существующее содержимое на странице следующее: 

    [!code-cshtml[Main](9-working-with-images/samples/sample6.cshtml)]

    Код использует `WebImage` вспомогательный метод для получения изображения с сервера. Создать путь к образу, используя ту же методику, используемый в предыдущих примерах для сохранения изображений, и передать этот путь при создании образа с помощью `WebImage`:

    [!code-javascript[Main](9-working-with-images/samples/sample7.js)]

    При обнаружении изображения, можно создать новый путь и имя файла, как это было сделано в предыдущих примерах. Перевернуть изображение, вызовите `FlipVertical` метод, а затем сохраните файл еще раз.

    Изображение снова отображается на странице с помощью `<img>` элемент с `src` атрибут `imagePath`.
3. Откройте страницу в браузере. Изображение для его *Photo2.jpg* отображается сверху вниз.
4. Обновите страницу или запросите страницу еще раз, чтобы увидеть, что изображение должно быть перевернуто справа от оператора копирования еще раз.

Для поворота изображения, используйте один и тот же код, но вместо вызова метода `FlipVertical` или `FlipHorizontal`, вы вызываете `RotateLeft` или `RotateRight`.

<a id="Adding_a_Watermark"></a>
## <a name="adding-a-watermark-to-an-image"></a>Добавление на изображение водяного знака

При добавлении изображения на веб-сайт, может потребоваться добавление предела в образе, прежде чем сохранить его или отобразить ее на странице. Люди часто используют водяные знаки, добавить сведения об авторских правах для изображения или для объявления имени бизнеса.

![[image]](9-working-with-images/_static/image5.jpg "ch9images 5.jpg")

1. Добавьте новую страницу с именем *Watermark.cshtml*.
2. Замените существующее содержимое на странице следующее: 

    [!code-cshtml[Main](9-working-with-images/samples/sample8.cshtml)]

    Этот код похож код в *FlipImage.cshtml* страницы из ранее (несмотря на то, что этот раз *Photo3.jpg* файла). Чтобы добавить водяной знак, необходимо вызвать `WebImage` вспомогательного приложения `AddTextWatermark` метод перед сохранением образа. В вызове `AddTextWatermark`, передать текст &quot;Мои водяного знака&quot;, задать цвет шрифта на желтый и значением Arial семейство шрифтов. (Несмотря на то, что это не показано, `WebImage` вспомогательный также позволяет указать непрозрачности, семейство шрифтов и размер и положение текста водяного знака.) При сохранении изображения оно не должно быть только для чтения.

    Как вы уже видели, изображение отображается на странице с помощью `<img>` элемент с атрибутом src значение `@imagePath`.
3. Откройте страницу в браузере. Обратите внимание на текст «My водяной знак» в правом верхнем углу изображения.

<a id="Using_an_Image_as_a_Watermark"></a>
## <a name="using-an-image-as-a-watermark"></a>Использование изображения в качестве водяного знака

Вместо использования текст для водяного знака, можно использовать другое изображение. Люди иногда использовать образы, такие как эмблема компании как водяной знак или они водяной знак вместо текста для сведения об авторских правах.

![[image]](9-working-with-images/_static/image6.jpg "ch9images 6.jpg")

1. Добавьте новую страницу с именем *ImageWatermark.cshtml*.
2. Добавить изображение в *образы* папке можно использовать в качестве эмблемы, а также переименовать изображение *MyCompanyLogo.jpg*. Этот образ должен быть изображения, как можно четко видеть, когда он имеет значение 80 пикселей в ширину и 20 пикселов в высоту.
3. Замените существующее содержимое на странице следующее: 

    [!code-cshtml[Main](9-working-with-images/samples/sample9.cshtml)]

    Это другой вариант на коде из предыдущих примерах. В этом случае Вы вызовите `AddImageWatermark` добавить изображение водяного знака в целевой образ (*Photo3.jpg*) перед сохранением образа. При вызове `AddImageWatermark`, задайте его ширину 80 пикселей и высоту, до 20 пикселей. *MyCompanyLogo.jpg* изображение горизонтально по центру и выравнивается вертикально в нижней части целевой образ. Прозрачность имеет значение 100% и заполнение, равными 10 пикселям. Если изображение водяного знака больше, чем конечный образ, ничего не происходит. Если изображение водяного знака больше, чем конечный образ и установить заполнение для изображений водяного знака до нуля, водяной знак учитывается.

    Как и раньше, отображения изображения с помощью `<img>` элемент и динамического `src` атрибута.
4. Откройте страницу в браузере. Обратите внимание, что изображение водяного знака в нижней части основного изображения.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Дополнительные ресурсы


[Работа с файлами на сайте страниц ASP.NET Web](https://go.microsoft.com/fwlink/?LinkId=202896)

[Введение в программирование с использованием синтаксиса Razor веб-страниц ASP.NET](https://go.microsoft.com/fwlink/?LinkID=251587)
