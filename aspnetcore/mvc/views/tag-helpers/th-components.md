---
title: Вспомогательные компоненты тегов в ASP.NET Core
author: scottaddie
description: Сведения о вспомогательных компонентах тегов и его использовании в ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: fb0bda0cf8d225df4c58ae43f81ed0dce10c1adc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587155"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Вспомогательные компоненты тегов в ASP.NET Core

Авторы: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie) и [Хасан Фияз Бин](https://github.com/fiyazbinhasan) (Hasan Fiyaz Bin)

Вспомогательный компонент тегов позволяет на основе условий изменять или добавлять элементы HTML из серверного кода. Эта функция доступна в ASP.NET Core 2.0 и более поздних версий.

ASP.NET Core включает в себя два встроенных вспомогательных компонента тегов: `head` и `body`. Они находятся в <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> пространстве имен и могут использоваться как в MVC, так и на Razor страницах. Вспомогательные компоненты тегов не нужно регистрировать в *_ViewImports.cshtml*.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Варианты использования

Два распространенных варианта для использования вспомогательных компонентов тегов:

1. [Внедрение `<link>` в `<head>`.](#inject-into-html-head-element)
1. [Внедрение `<script>` в `<body>`.](#inject-into-html-body-element)

Эти варианты использования описаны в следующих разделах.

### <a name="inject-into-html-head-element"></a>Внедрение в элемент HTML "head"

Внутри элемента HTML `<head>` файлы CSS обычно импортируются с помощью элемента HTML `<link>`. Следующий код позволяет вставить элемент `<link>` в элемент `<head>` с помощью вспомогательного компонента тегов `head`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

В приведенном выше коде:

* Объект `AddressStyleTagHelperComponent` реализует интерфейс <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Абстракция:
  * Позволяет инициализировать класс с <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Позволяет использовать вспомогательные компоненты тегов для добавления или изменения элементов HTML.
* Свойство <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> определяет порядок отображения компонентов. Свойство `Order` необходимо, если вспомогательные компоненты тегов используются в приложении несколько раз.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> сравнивает свойство <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> контекста выполнения со значением `head`. Если сравнение возвращает результат TRUE, содержимое поля `_style` внедряется в элемент HTML `<head>`.

### <a name="inject-into-html-body-element"></a>Внедрение в элемент HTML "body"

Вспомогательный компонент тэгов `body` позволяет внедрять элемент `<script>` в элемент `<body>`. Этот метод демонстрируется в приведенном ниже коде.

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Для хранения элемента `<script>` используется отдельный HTML-файл. Этот HTML-файл делает код более чистым и удобным в обслуживании. С помощью приведенного выше кода считывается содержимое *TagHelpers/Templates/AddressToolTipScript.html* и к нему присоединяются выходные данные вспомогательного компонента тегов. Файл *AddressToolTipScript.html* содержит следующую разметку:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Приведенный выше код привязывает [мини-приложение подсказки начальной загрузки](https://getbootstrap.com/docs/3.3/javascript/#tooltips) к любому элементу `<address>`, который содержит атрибут `printable`. Результат можно проследить при наведении указателя мыши на соответствующий элемент.

## <a name="register-a-component"></a>Регистрация компонента

Вспомогательные компоненты тегов необходимо добавить в коллекцию вспомогательных компонентов тегов приложения. Добавить его в коллекцию можно тремя способами:

* [регистрация с помощью контейнера служб](#registration-via-services-container);
* [Регистрация с помощью Razor файла](#registration-via-razor-file)
* [регистрация с помощью модели страницы или контроллера](#registration-via-page-model-or-controller).

### <a name="registration-via-services-container"></a>Регистрация с помощью контейнера служб

Если <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> не управляет классом вспомогательного компонента тегов, класс нужно зарегистрировать в системе [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection). Следующий код `Startup.ConfigureServices` позволяет зарегистрировать классы `AddressStyleTagHelperComponent` и `AddressScriptTagHelperComponent` с [ограниченным временем существования](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Регистрация с помощью Razor файла

Если вспомогательный компонент тега не зарегистрирован в режиме внедрения, его можно зарегистрировать на Razor странице страниц или в представлении MVC. Этот метод используется для управления внедренной разметкой и порядком выполнения компонента из Razor файла.

`ITagHelperComponentManager` позволяет добавлять вспомогательные компоненты тегов и (или) удалять их из приложения. Этот метод демонстрируется в следующем коде на примере `AddressTagHelperComponent`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

В приведенном выше коде:

* Директива `@inject` предоставляет экземпляр `ITagHelperComponentManager`. Экземпляр присваивается переменной с именем `manager` для доступа в Razor файле.
* Экземпляр `AddressTagHelperComponent` добавляется в коллекцию вспомогательных компонентов тегов.

В `AddressTagHelperComponent` вносятся изменения для создания конструктора, который принимает параметры `markup` и `order`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Предоставленный параметр `markup` используется в `ProcessAsync` следующим образом:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Регистрация с помощью модели страницы или контроллера

Если вспомогательный компонент тега не зарегистрирован в ходе внедрения, его можно зарегистрировать на Razor странице страницы или в контроллере MVC. Этот метод полезен для отделения логики C# от Razor файлов.

Для доступа к экземпляру `ITagHelperComponentManager` внедряется конструктор. Вспомогательный компонент тегов добавляется в коллекцию вспомогательных компонентов тегов. RazorЭта методика показана в следующей модели страниц `AddressTagHelperComponent` :

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

В приведенном выше коде:

* Для доступа к экземпляру `ITagHelperComponentManager` внедряется конструктор.
* Экземпляр `AddressTagHelperComponent` добавляется в коллекцию вспомогательных компонентов тегов.

## <a name="create-a-component"></a>Создание компонента

Чтобы создать пользовательский вспомогательный компонент тегов, выполните следующие действия:

* Создайте открытый класс, который наследует от <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Примените [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) к классу атрибут. Укажите имя целевого элемента HTML.
* *Необязательно*: применение [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) атрибута к классу для подавления отображения типа в IntelliSense.

Следующий код позволяет создать пользовательский вспомогательный компонент тегов, который работает с элементом HTML `<address>`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

С помощью настраиваемого вспомогательного компонента тегов `address` внедрите HTML-разметку, как указано ниже:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

Приведенный выше метод `ProcessAsync` позволяет внедрить предоставленный в <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> HTML-код в соответствующий элемент `<address>`. Внедрение происходит при следующих условиях:

* Свойство `TagName` контекста выполнения совпадает со значением `address`.
* Соответствующий элемент `<address>` имеет атрибут `printable`.

Например, инструкция `if` возвращает значение TRUE при обработке следующего элемента `<address>`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
