---
title: Создание и использование компонентов ASP.NET Core Razor
author: guardrex
description: Узнайте, как создавать и использовать компоненты Razor, включая способы привязки к данным, обработки событий и управления жизненным циклом компонентов.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/02/2019
uid: blazor/components
ms.openlocfilehash: 43457bffd748ebba68cc86d33fdeb98dc419704b
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68948434"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Создание и использование компонентов ASP.NET Core Razor

[Люк ЛаСаМ](https://github.com/guardrex) и [Даниэль Roth)](https://github.com/danroth27)

[Просмотреть или скачать образец кода](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Приложения блазор создаются с помощью *компонентов*. Компонент — это автономный фрагмент пользовательского интерфейса, например страница, диалоговое окно или форма. Компонент включает разметку HTML и логику обработки, необходимую для вставки данных или реагирования на события пользовательского интерфейса. Компоненты являются гибкими и легковесными. Они могут быть вложенными, повторно использованы и совместно использоваться проектами.

## <a name="component-classes"></a>Классы компонентов

Компоненты реализуются в файлах компонентов [Razor](xref:mvc/views/razor) ( *. Razor*) с помощью комбинации C# и HTML-разметки. Компонент в Блазор формально называется *компонентом Razor*.

Компоненты можно создавать с помощью расширения файла *. cshtml* . Используйте свойство MSBuild в файле проекта для обнаружения файлов Component *. cshtml.* `_RazorComponentInclude` Например, приложение, которое указывает, что все файлы *. cshtml* в папке *pages* должны рассматриваться как файлы компонентов Razor:

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

Пользовательский интерфейс для компонента определяется с помощью HTML. Логика динамического отображения (например выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием [Razor](xref:mvc/views/razor). При компиляции приложения логика разметки и C# отрисовки HTML преобразуется в класс компонента. Имя созданного класса соответствует имени файла.

Элементы класса компонента определяются в блоке `@code`. `@code` В блоке состояние компонента (свойства, поля) указывается с помощью методов обработки событий или для определения другой логики компонента. Допускается более одного `@code` блока.

> [!NOTE]
> В предыдущих предварительных версиях ASP.NET Core 3,0 `@functions` блоки использовались для тех же целей, что `@code` и блоки в компонентах Razor. `@functions`блоки продолжают функционировать в компонентах Razor, но мы рекомендуем использовать `@code` блок в ASP.NET Core 3,0 Preview 6 или более поздней версии.

Члены компонента можно использовать как часть логики отрисовки компонента с помощью C# выражений, начинающихся с `@`. Например, C# поле подготавливается к просмотру путем `@` добавления префикса к имени поля. В следующем примере вычисляется и выводится следующее:

* `_headingFontStyle`к значению свойства CSS для `font-style`.
* `_headingText`к содержимому `<h1>` элемента.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

После первоначального отображения компонента Компонент повторно создает дерево рендеринга в ответ на события. Затем блазор сравнивает новое дерево отрисовки с предыдущим и применяет любые изменения к модель DOMу (DOM) браузера.

Компоненты являются обычными C# классами и могут быть помещены в любое место внутри проекта. Компоненты, создающие веб-страницы, обычно находятся в папке *страниц* . Компоненты, не являющиеся страницами, часто помещаются в *общую* папку или пользовательскую папку, добавленную в проект. Чтобы использовать пользовательскую папку, добавьте пространство имен пользовательской папки либо в родительский компонент, либо в файл *_Imports. Razor* приложения. Например, следующее пространство имен делает компоненты в папке *Components* доступной, когда корневое пространство имен приложения имеет `WebApplication`следующий уровень:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Интеграция компонентов в Razor Pages и приложения MVC

Используйте компоненты с существующими Razor Pages и приложениями MVC. Нет необходимости переписывать существующие страницы или представления для использования компонентов Razor. При отображении страницы или представления компоненты предварительно отображаются.

Чтобы отобразить компонент из страницы или представления, используйте `RenderComponentAsync<TComponent>` вспомогательный метод HTML:

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

Хотя страницы и представления могут использовать компоненты, наоборот это не так. Компоненты не могут использовать сценарии представления и страницы, такие как частичные представления и разделы. Чтобы использовать логику из частичного представления в компоненте, разнесите логику частичного представления в компонент.

Дополнительные сведения о подготовке компонентов к просмотру и управлении состоянием компонентов в приложениях блазор на стороне сервера см. в <xref:blazor/hosting-models> статье.

## <a name="using-components"></a>Использование компонентов

Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса HTML-элементов. Разметка для использования компонента выглядит как тег HTML с именем, соответствующем типу компонента.

Следующая разметка в *index. Razor* визуализирует `HeadingComponent` экземпляр:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Components/хеадингкомпонент. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a>Параметры компонентов

Компоненты могут иметь *Параметры компонента*, которые определяются с помощью свойств (обычно *не являющихся открытыми*) в `[Parameter]` классе Component с атрибутом. Используйте атрибуты, чтобы указать аргументы для компонента в разметке.

*Components/чилдкомпонент. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

В следующем примере `ParentComponent` `Title` свойство устанавливает значение свойства объекта `ChildComponent`.

*Pages/паренткомпонент. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Дочернее содержимое

Компоненты могут устанавливать содержимое другого компонента. Назначение компонента предоставляет содержимое между тегами, задающих принимающий компонент.

В следующем примере объект `ChildComponent` `ChildContent` имеет свойство, представляющее объект `RenderFragment`, который представляет сегмент пользовательского интерфейса для отрисовки. Значение `ChildContent` размещается в разметке компонента, где должно быть визуализировано содержимое. Значение `ChildContent` получается от родительского компонента и отображается внутри `panel-body`панели начальной загрузки.

*Components/чилдкомпонент. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Свойству, получающему `RenderFragment` содержимое, необходимо `ChildContent` присвоить имя по соглашению.

Следующие `ParentComponent` данные могут предоставлять содержимое для подготовки к `ChildComponent` просмотру путем `<ChildComponent>` размещения содержимого внутри тегов.

*Pages/паренткомпонент. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Атрибуты Сплаттинг и произвольные параметры

Компоненты могут записывать и отображать дополнительные атрибуты в дополнение к объявленным параметрам компонента. Дополнительные атрибуты можно записать в словарь, а затем *сплаттед* на элемент при подготовке компонента к просмотру с помощью [@attributes](xref:mvc/views/razor#attributes) директивы Razor. Этот сценарий полезен при определении компонента, который создает элемент разметки, поддерживающий разнообразные настройки. Например, может быть утомительно определять атрибуты отдельно для `<input>` , который поддерживает множество параметров.

В `<input>` следующем примере первый элемент (`id="useIndividualParams"`) использует индивидуальные параметры компонента, а второй `<input>` элемент (`id="useAttributesDict"`) использует атрибут Сплаттинг:

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    private string Maxlength { get; set; } = "10";

    [Parameter]
    private string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    private string Required { get; set; } = "required";

    [Parameter]
    private string Size { get; set; } = "50";

    [Parameter]
    private Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "true" },
            { "size", "50" }
        };
}
```

Тип параметра должен реализовываться `IEnumerable<KeyValuePair<string, object>>` со строковыми ключами. В `IReadOnlyDictionary<string, object>` этом сценарии также используется параметр using.

Отображаемые `<input>` элементы, использующие оба подхода, идентичны:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="true"
       size="50">
```

Чтобы принять произвольные атрибуты, определите параметр компонента с помощью `[Parameter]` атрибута `CaptureUnmatchedValues` со свойством, имеющим `true`значение:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    private Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues` Свойство в`[Parameter]` позволяет параметру сопоставлять все атрибуты, не соответствующие ни одному другому параметру. Компонент может определять только один параметр с помощью `CaptureUnmatchedValues`. Тип свойства, используемый с `CaptureUnmatchedValues` параметром, должен быть `Dictionary<string, object>` назначен с помощью строковых ключей. `IEnumerable<KeyValuePair<string, object>>`Кроме `IReadOnlyDictionary<string, object>` того, в этом сценарии также есть параметры.

## <a name="data-binding"></a>Привязка данных

Привязка данных как к компонентам, так и к элементам DOM [@bind](xref:mvc/views/razor#bind) осуществляется с помощью атрибута. В следующем примере `_italicsCheck` поле привязывается к установленному флажку.

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

Если флажок установлен и снят, значение свойства обновляется до `true` и `false`соответственно.

Флажок обновляется в пользовательском интерфейсе только при подготовке компонента к просмотру, а не в ответ на изменение значения свойства. Поскольку компоненты отображаются после выполнения кода обработчика событий, обновления свойств, как правило, немедленно отражаются в пользовательском интерфейсе.

Использование `@bind` `<input @bind="CurrentValue" />`со свойством (), по сути, эквивалентно следующему: `CurrentValue`

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

При подготовке компонента к `value` просмотру элемент входного элемента берется `CurrentValue` из свойства. Когда пользователь вводит текст в текстовое поле, `onchange` создается событие, `CurrentValue` а свойству присваивается измененное значение. На практике создание кода немного сложнее, так как `@bind` обрабатывает несколько случаев, когда выполняются преобразования типов. В принципе, `@bind` связывает текущее значение выражения `value` с атрибутом и обрабатывает изменения с помощью зарегистрированного обработчика.

`onchange` Помимо обработки[@bind-value:event](xref:mvc/views/razor#bind)событий с `@bind` синтаксисом, свойство или поле можно привязать с помощью [@bind-value](xref:mvc/views/razor#bind) других событий, `event` указав атрибут с параметром (). В следующем примере показано, `CurrentValue` как привязать свойство `oninput` к событию:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

В отличие `onchange`от, которое срабатывает при потере фокуса элементом `oninput` , срабатывает при изменении значения текстового поля.

**Строки формата**

Привязка данных работает со <xref:System.DateTime> строками формата [@bind:format](xref:mvc/views/razor#bind)с помощью. Другие выражения форматирования, такие как денежные или числовые форматы, в настоящее время недоступны.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Атрибут задает формат даты, `value` `<input>` применяемый к элементу. `@bind:format` Формат также используется для анализа значения при `onchange` возникновении события.

**Параметры компонента**

Привязка распознает параметры компонента, `@bind-{property}` где можно привязать значение свойства к разным компонентам.

Следующий дочерний компонент`ChildComponent`() `Year` имеет параметр компонента и `YearChanged` обратный вызов:

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`описывается в разделе [вложенный EventCallback](#eventcallback) .

Следующий родительский компонент использует `ChildComponent` и связывает `ParentYear` параметр `Year` из родительского компонента с параметром в дочернем компоненте:

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

При загрузке `ParentComponent` создается следующая разметка:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Если значение `ParentYear` свойства изменяется путем нажатия кнопки `ParentComponent`в `ChildComponent` , `Year` свойство элемента обновляется. Новое значение параметра `Year` отображается в пользовательском интерфейсе при его `ParentComponent` визуализации.

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Параметр является связываемым, так как содержит сопутствующее `YearChanged` событие, `Year` соответствующее типу параметра. `Year`

По соглашению `<ChildComponent @bind-Year="ParentYear" />` , по сути, эквивалентно написанию:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Как правило, свойство может быть привязано к соответствующему обработчику событий `@bind-property:event` с помощью атрибута. Например, свойство `MyProp` можно привязать к `MyEventHandler` с помощью следующих двух атрибутов:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Обработка событий

Компоненты Razor предоставляют функции обработки событий. Для атрибута HTML-элемента с `on{event}` именем (например, `onclick` и `onsubmit`) со значением, вводимым с помощью делегата, компоненты Razor рассматривают значение атрибута как обработчик событий. Имя атрибута всегда имеет формат [ @on{Event}](xref:mvc/views/razor#onevent).

Следующий код вызывает метод, `UpdateHeading` когда кнопка выбрана в пользовательском интерфейсе:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

Следующий код вызывает `CheckChanged` метод при изменении флажка в пользовательском интерфейсе:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Обработчики событий также могут быть асинхронными и <xref:System.Threading.Tasks.Task>возвращать. Нет необходимости вручную вызывать `StateHasChanged()`. Исключения регистрируются при их возникновении.

В следующем примере `UpdateHeading` метод вызывается асинхронно при выборе кнопки:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>Типы аргументов событий

Для некоторых событий разрешены типы аргументов событий. Если доступ к одному из этих типов событий не нужен, в вызове метода это не требуется.

Поддерживаемые [уиевентаргс](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs) показаны в следующей таблице.

| событие | Класс |
| ----- | ----- |
| буфер обмена | `UIClipboardEventArgs` |
| Переместить  | `UIDragEventArgs`используется для хранения перетаскиваемых данных во время операции перетаскивания и может содержать один или несколько `UIDataTransferItem`. &ndash; `DataTransfer` `UIDataTransferItem`представляет один элемент данных перетаскивания. |
| Error | `UIErrorEventArgs` |
| Фокус | `UIFocusEventArgs`Не включает поддержку для `relatedTarget`. &ndash; |
| Изменение`<input>` | `UIChangeEventArgs` |
| Клавиатура | `UIKeyboardEventArgs` |
| Мышь | `UIMouseEventArgs` |
| Указатель мыши | `UIPointerEventArgs` |
| Колесо мыши | `UIWheelEventArgs` |
| Ход выполнения | `UIProgressEventArgs` |
| Сенсорные технологии | `UITouchEventArgs`&ndash; представляетоднуточкуконтактанаустройствес`UITouchPoint` сенсорным вводом. |

Сведения о свойствах и поведении событий в приведенной выше таблице см. в разделе [классы EventArgs в исходном справочнике](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src).

### <a name="lambda-expressions"></a>Лямбда-выражения

Лямбда-выражения также могут использоваться:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов. В следующем примере создаются три кнопки, каждый из которых вызывает `UpdateHeading` передачу аргумента события (`UIMouseEventArgs`) и номер кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> **Не** используйте переменную цикла (`i`) в `for` цикле непосредственно в лямбда-выражении. В противном случае одна и та же переменная используется всеми `i`лямбда-выражениями, которые вызывают одинаковое значение во всех лямбдаах. Всегда запишите свое значение в локальную переменную (`buttonNumber` в предыдущем примере), а затем используйте ее.

### <a name="eventcallback"></a>Вложенный EventCallback

Распространенным сценарием с вложенными компонентами является желание запускать метод родительского компонента при возникновении&mdash;события дочернего компонента, например `onclick` при возникновении события в дочернем элементе. Чтобы предоставить события для компонентов, используйте `EventCallback`. Родительский компонент может назначить метод обратного вызова для дочернего `EventCallback`компонента.

В примере приложения `EventCallback` демонстрируется настройка `onclick` обработчика кнопки на получение делегата из примера `ParentComponent`. `ChildComponent` Вводится с помощью `UIMouseEventArgs` ,`onclick` что подходит для события с периферийного устройства. `EventCallback`

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Задает для дочернего `EventCallback<T>` элемента метод:`ShowMessage` `ParentComponent`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

При выборе кнопки в `ChildComponent`:

* `ParentComponent` Вызывается`ShowMessage` метод. `messageText`обновляется и отображается в `ParentComponent`.
* Вызов `StateHasChanged` метода не требуется в методе обратного вызова (`ShowMessage`). `StateHasChanged`вызывается автоматически для перевизуализации `ParentComponent`компонента, как и в случае, когда дочерние события активируют компонент в обработчиках событий, которые выполняются внутри дочернего элемента.

`EventCallback`и `EventCallback<T>` разрешите асинхронные делегаты. `EventCallback<T>`является строго типизированным и требует определенного типа аргумента. `EventCallback`слабо типизирован и допускает любой тип аргумента.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

Вызовите `EventCallback<T>` `InvokeAsync` или с параметром и <xref:System.Threading.Tasks.Task>дождаться: `EventCallback`

```csharp
await callback.InvokeAsync(arg);
```

Используйте `EventCallback` и`EventCallback<T>` для обработки событий и параметров компонента привязки.

Предпочитать строго типизированный `EventCallback<T>`. `EventCallback` `EventCallback<T>`обеспечивает лучшую реакцию на ошибки для пользователей компонента. Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным. Используется `EventCallback` при отсутствии значения, переданного обратному вызову.

## <a name="capture-references-to-components"></a>Запись ссылок на компоненты

Ссылки на компоненты предоставляют способ ссылки на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра, например `Show` или. `Reset` Чтобы записать ссылку на компонент, добавьте [@ref](xref:mvc/views/razor#ref) атрибут к дочернему компоненту, а затем определите поле с тем же именем и типом, что и у дочернего компонента.

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

При подготовке компонента к просмотру `loginDialog` поле заполняется `MyLoginDialog` экземпляром дочернего компонента. Затем можно вызывать методы .NET в экземпляре компонента.

> [!IMPORTANT]
> Переменная заполняется только после подготовки компонента, и ее выходные данные `MyLoginDialog` включают элемент. `loginDialog` До этого момента нет никаких ссылок на. Чтобы управлять ссылками на компоненты после завершения подготовки компонента к просмотру `OnAfterRenderAsync` , `OnAfterRender` используйте методы или.

При захвате ссылок на компоненты используется аналогичный синтаксис для [записи ссылок на элементы](xref:blazor/javascript-interop#capture-references-to-elements), но это не функция [взаимодействия JavaScript](xref:blazor/javascript-interop) . Ссылки на компоненты не передаются&mdash;в код JavaScript, они используются только в коде .NET.

> [!NOTE]
> **Не** используйте ссылки на компоненты для изменения состояния дочерних компонентов. Вместо этого используйте обычные декларативные параметры для передачи данных дочерним компонентам. Использование обычных декларативных параметров приводит к тому, что дочерние компоненты автоматически визуализируются в нужное время.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Использование \@ключа для управления сохранением элементов и компонентов

При последующем изменении списка элементов или компонентов, а также элементов или компонентов алгоритм сравнения Блазор должен решить, какие из предыдущих элементов или компонентов могут быть сохранены и как объекты модели должны сопоставляться с ними. Обычно этот процесс выполняется автоматически и его можно игнорировать, но в некоторых случаях может потребоваться управление процессом.

Рассмотрим следующий пример.

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

Содержимое `People` коллекции может изменяться при вставке, удалении или повторном упорядочении записей. Когда компонент перерисовывается, `<DetailsEditor>` компонент может измениться для получения различных `Details` значений параметров. Это может привести к более сложной визуализации, чем ожидалось. В некоторых случаях перерисовка может привести к появлению видимых различий в поведении, таких как потеря фокуса элементов.

Процесс сопоставления можно контролировать с помощью `@key` атрибута директивы. `@key`заставляет алгоритм сравнения гарантировать сохранение элементов или компонентов на основе значения ключа:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

При изменении `<DetailsEditor>` `person` коллекции алгоритм сравнения удерживает связи между экземплярами и экземплярами. `People`

* Если объект `Person` удаляется `People` из списка, то из пользовательского интерфейса `<DetailsEditor>` удаляется только соответствующий экземпляр. Другие экземпляры остаются без изменений.
* Если в `Person` какой-либо позиции в списке вставляется элемент, то `<DetailsEditor>` в соответствующую позиции вставляется один новый экземпляр. Другие экземпляры остаются без изменений.
* При `Person` повторном упорядочении записей соответствующие `<DetailsEditor>` экземпляры сохраняются и повторно упорядочиваются в пользовательском интерфейсе.

В некоторых сценариях использование `@key` сводится к уменьшению сложности повторного отображения и позволяет избежать потенциальных проблем с элементами, изменяющимися с отслеживанием состояния DOM, например положением фокуса.

> [!IMPORTANT]
> Ключи являются локальными для каждого элемента или компонента контейнера. Ключи не сравниваются глобально по всему документу.

### <a name="when-to-use-key"></a>Когда следует использовать \@ключ

Обычно имеет смысл использовать `@key` каждый раз при подготовке списка (например, `@foreach` в блоке), а подходящее `@key`значение существует для определения.

Кроме того, можно `@key` использовать, чтобы предотвратить блазор с сохранением поддерева элемента или компонента при изменении объекта:

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

Если `@currentPerson` `<div>` изменяется`@key` , директива атрибута заставляет блазор отбросить все и его потомки и перестроить поддерево в пользовательском интерфейсе с помощью новых элементов и компонентов. Это может быть полезно, если необходимо гарантировать, что при `@currentPerson` изменении состояние пользовательского интерфейса не сохраняется.

### <a name="when-not-to-use-key"></a>Когда не следует использовать \@ключ

При использовании различий в `@key`производительности возникают затраты на производительность. Затраты на производительность не слишком велики, но указывают `@key` только в том случае, если управление правилами сохранения элементов или компонентов выгодно для приложения.

Даже если `@key` не используется, блазор сохраняет дочерние элементы и экземпляры компонентов насколько это возможно. Единственным преимуществом использования `@key` является управление *тем, как* экземпляры модели сопоставляются с сохраненными экземплярами компонента, а не алгоритмом сравнения, который выбирает сопоставление.

### <a name="what-values-to-use-for-key"></a>Какие значения следует использовать для \@ключа

Как правило, имеет смысл указать одно из следующих видов значений для `@key`:

* Экземпляры объектов Model (например, `Person` экземпляр, как в предыдущем примере). Это гарантирует сохранение на основе равенства ссылок на объекты.
* Уникальные идентификаторы (например, значения первичного ключа типа `int`, `string`или `Guid`).

Старайтесь не предоставлять значение, которое может неожиданно конфликтовать. Если `@key="@someObject.GetHashCode()"` указан аргумент, могут возникнуть непредвиденные конфликты, так как хэш-коды несвязанных объектов могут быть одинаковыми. Если конфликтные `@key` значения запрашиваются в пределах одного родителя `@key` , значения не будут учитываться.

## <a name="lifecycle-methods"></a>Методы жизненного цикла

`OnInitAsync`и `OnInit` выполняют код для инициализации компонента. Для выполнения асинхронной операции используйте `OnInitAsync` `await` и ключевое слово для операции:

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

Для синхронной операции используйте `OnInit`:

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync`и `OnParametersSet` вызываются, когда компонент получил параметры от родительского элемента, и значения присваиваются свойствам. Эти методы выполняются после инициализации компонента и при каждом отображении компонента:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync`и `OnAfterRender` вызываются после завершения подготовки компонента к просмотру. В этот момент заполнены ссылки на элементы и компоненты. Используйте этот этап для выполнения дополнительных шагов инициализации с помощью готового к просмотру содержимого, например для активации сторонних библиотек JavaScript, которые работают с визуализированными элементами DOM.

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>Обработка незавершенных асинхронных действий при отрисовке

Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до подготовки компонента к просмотру. Объекты могут быть `null` заполнены или незаполнены данными во время выполнения метода жизненного цикла. Предоставьте логику отрисовки для подтверждения инициализации объектов. Отрисовывает элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты `null`являются объектами.

В компоненте `OnInitAsync` шаблонов блазор переопределяется в асичронаусли получения данных прогноза (`forecasts`). `FetchData` Если `forecasts` параметр `null`имеет значение, пользователю выводится сообщение о загрузке. После завершения `OnInitAsync` возврата по завершении компонент перерисовывается с обновленным состоянием. `Task`

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Выполнить код до установки параметров

`SetParameters`можно переопределить для выполнения кода перед установкой параметров:

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Если `base.SetParameters` не вызывается, Пользовательский код может интерпретировать значение входящих параметров любым необходимым образом. Например, входящие параметры не обязательно должны быть назначены свойствам класса.

### <a name="suppress-refreshing-of-the-ui"></a>Отключить обновление пользовательского интерфейса

`ShouldRender`можно переопределить, чтобы отключить обновление пользовательского интерфейса. Если реализация возвращает `true`, Пользовательский интерфейс обновляется. Даже если `ShouldRender` переопределяется, компонент всегда первоначально готовится к просмотру.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Освобождение компонентов с помощью IDisposable

Если компонент реализует <xref:System.IDisposable>, [метод Dispose](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса. Следующий компонент использует `@implements IDisposable` `Dispose` и метод:

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>Маршрутизация

Маршрутизация в Блазор достигается путем предоставления шаблона маршрута для каждого доступного компонента в приложении.

При компиляции файла Razor с `@page` директивой созданному классу <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> присваивается Указание шаблона маршрута. Во время выполнения маршрутизатор ищет классы компонентов с помощью `RouteAttribute` и отображает, какой из компонентов имеет шаблон маршрута, соответствующий запрошенному URL-адресу.

К компоненту можно применить несколько шаблонов маршрутов. Следующий компонент отвечает на запросы `/BlazorRoute` и: `/DifferentBlazorRoute`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Параметры маршрута

Компоненты могут принимать параметры маршрута из шаблона маршрута, указанного в `@page` директиве. Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента.

*Компонент параметра маршрута*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

Необязательные параметры не поддерживаются `@page` , поэтому в приведенном выше примере применяются две директивы. Первый позволяет переходить к компоненту без параметра. Вторая `@page` директива `{text}` принимает параметр Route и присваивает значение `Text` свойству.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Наследование базового класса для взаимодействия с кодом программной части

Файлы компонентов сочетают разметку HTML C# и обрабатывают код в одном файле. `@inherits` Директиву можно использовать для предоставления блазор приложений с «кодом программной части», который отделяет разметку компонента от кода обработки.

В [примере приложения](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) показано, как компонент может наследовать базовый класс, `BlazorRocksBase`чтобы предоставить свойства и методы компонента.

*Pages/блазорроккс. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.CS*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

Базовый класс должен быть производным от `ComponentBase`.

## <a name="import-components"></a>Импорт компонентов

Пространство имен компонента, созданного с помощью Razor, основано на следующих:

* Проект `RootNamespace`.
* Путь от корня проекта к компоненту. Например, `ComponentsSample/Pages/Index.razor` находится в пространстве имен `ComponentsSample.Pages`. Компоненты следуют C# правилам привязки имен. В случае *index. Razor*все компоненты в той же папке, *страницах*и родительской папке *компонентссампле*находятся в области действия.

Компоненты, определенные в другом пространстве имен, могут быть добавлены в область с помощью директивы [ \@using](xref:mvc/views/razor#using) Razor.

Если в папке `ComponentsSample/Shared/`существует `NavMenu.razor`другой компонент, то этот компонент можно использовать в `Index.razor` со следующей `@using` инструкцией:

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

На компоненты также можно ссылаться с помощью полных имен, что устраняет необходимость в [ \@](xref:mvc/views/razor#using) директиве using:

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> `global::` Квалификация не поддерживается.
>
> Импорт компонентов с псевдонимами `using` операторов (например, `@using Foo = Bar`) не поддерживается.
>
> Частично определенные имена не поддерживаются. Например, добавление `@using ComponentsSample` и создание ссылок `NavMenu.razor` с `<Shared.NavMenu></Shared.NavMenu>` помощью не поддерживается.

## <a name="conditional-html-element-attributes"></a>Атрибуты условного HTML-элемента

Атрибуты HTML-элемента условно подготавливаются в зависимости от значения .NET. Если значение равно `false` или `null`, то атрибут не подготавливается к просмотру. Если значение равно `true`, атрибут отображается в виде сворачивания.

В следующем примере определяет, `IsCompleted` отображается ли `checked` в разметке элемента:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

Если `IsCompleted` имеет `true`значение, флажок отображается следующим образом:

```html
<input type="checkbox" checked />
```

Если `IsCompleted` имеет `false`значение, флажок отображается следующим образом:

```html
<input type="checkbox" />
```

Дополнительные сведения см. в разделе <xref:mvc/views/razor>.

## <a name="raw-html"></a>Необработанный HTML

Строки обычно подготавливаются с помощью текстовых узлов DOM, что означает, что любая разметка, которую они могут содержать, игнорируется и обрабатывается как литеральный текст. Для отрисовки необработанного HTML-кода заключите `MarkupString` содержимое HTML в значение. Значение анализируется как HTML или SVG и вставляется в модель DOM.

> [!WARNING]
> Визуализация необработанного HTML-кода, созданного из любого ненадежного источника, является **угрозой безопасности** , и ее следует избегать.

В следующем примере показано использование `MarkupString` типа для добавления блока статического HTML-содержимого в отображаемые выходные данные компонента:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Шаблонные компоненты

Шаблонные компоненты — это компоненты, принимающие один или несколько шаблонов пользовательского интерфейса в качестве параметров, которые затем можно использовать как часть логики отрисовки компонента. Шаблонные компоненты позволяют создавать более высокие компоненты более высокого уровня, чем обычные компоненты. Ниже приведены несколько примеров.

* Компонент таблицы, позволяющий пользователю указывать шаблоны для заголовков, строк и нижних колонтитулов таблицы.
* Компонент списка, позволяющий пользователю указать шаблон для отрисовки элементов в списке.

### <a name="template-parameters"></a>Параметры шаблона

Шаблонный компонент определяется путем указания одного или нескольких параметров компонента типа `RenderFragment` или. `RenderFragment<T>` Фрагмент отрисовки представляет сегмент пользовательского интерфейса для отрисовки. `RenderFragment<T>`принимает параметр типа, который может быть указан при вызове фрагмента прорисовки.

`TableTemplate`см

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

При использовании компонента шаблона можно указать параметры шаблона с помощью дочерних элементов, совпадающих с именами параметров (`TableHeader` и `RowTemplate` в следующем примере):

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>Параметры контекста шаблона

Аргументы компонента типа `RenderFragment<T>` , переданные как элементы, имеют неявный параметр с именем `context` (например, из предыдущего `@context.PetId`примера кода,), но можно изменить имя параметра с `Context` помощью атрибута дочернего элемента дерев. В следующем примере `RowTemplate` `Context` атрибут элемента задает `pet` параметр:

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

Кроме того, можно указать `Context` атрибут для элемента Component. Указанный `Context` атрибут применяется ко всем указанным параметрам шаблона. Это может быть полезно, если необходимо указать имя параметра содержимого для неявного дочернего содержимого (без обертывания дочернего элемента). В следующем примере `Context` атрибут отображается `TableTemplate` в элементе и применяется ко всем параметрам шаблона:

```cshtml
<TableTemplate Items="@pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>Универсальные типы компонентов

Шаблонные компоненты часто вводятся в универсальном виде. Например, универсальный `ListViewTemplate` компонент можно использовать для отображения `IEnumerable<T>` значений. Чтобы определить универсальный компонент, используйте `@typeparam` директиву для указания параметров типа:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

При использовании компонентов универсальной типизации параметр типа выводится, если это возможно:

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

В противном случае параметр типа необходимо явно указать с помощью атрибута, совпадающего с именем параметра типа. В следующем примере `TItem="Pet"` указывает тип:

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Каскадные значения и параметры

В некоторых сценариях неудобно передавать данные из компонента-предка в дочерний компонент с помощью [параметров компонента](#component-parameters), особенно при наличии нескольких слоев компонентов. Каскадные значения и параметры позволяют решить эту проблему, предоставляя удобный способ, позволяющий компоненту-предку предоставить значение всем его дочерним компонентам. Каскадные значения и параметры также предоставляют подход для координации компонентов.

### <a name="theme-example"></a>Пример темы

В следующем примере из примера приложения `ThemeInfo` класс указывает сведения о теме для перетекания иерархии компонентов, чтобы все кнопки в пределах данной части приложения совпадали с тем же стилем.

*Уисемеклассес/themeinfo указывает расположение. CS*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Компонент-предок может предоставлять каскадное значение с помощью компонента каскадного значения. `CascadingValue` Компонент заключает поддерево иерархии компонентов и предоставляет одно значение всем компонентам в этом поддереве.

Например, в примере приложения указываются сведения о теме`ThemeInfo`() в одном из макетов приложения в виде каскадного параметра для всех компонентов, составляющих текст `@Body` макета свойства. `ButtonClass`присваивается значение `btn-success` в компоненте макета. Любой компонент-потомок может использовать это свойство через `ThemeInfo` каскадный объект.

`CascadingValuesParametersLayout`см

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Чтобы использовать каскадные значения, компоненты объявляют каскадные параметры с помощью `[CascadingParameter]` атрибута или на основе значения имени строки:

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

Привязка со значением имени строки уместна, если имеется несколько каскадных значений одного типа и необходимо отличать их друг от друга в одном поддереве.

Каскадные значения привязываются к каскадным параметрам по типу.

В примере приложения `CascadingValuesParametersTheme` компонент привязывает `ThemeInfo` каскадное значение к каскадному параметру. Параметр используется для задания класса CSS для одной из кнопок, отображаемых компонентом.

`CascadingValuesParametersTheme`см

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a>Пример Табсет

Каскадные параметры также позволяют компонентам работать в иерархии компонентов. Например, рассмотрим следующий пример *табсет* в примере приложения.

Пример приложения имеет интерфейс, `ITab` который реализуется с помощью вкладок:

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

Компонент использует компонент, который содержит несколько `Tab` компонентов: `TabSet` `CascadingValuesParametersTabSet`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

Дочерние `Tab` компоненты явно не передаются в `TabSet`качестве параметров в. Вместо этого дочерние `Tab` компоненты являются частью дочернего содержимого `TabSet`. Тем не менее `TabSet` , по-прежнему необходимо узнать `Tab` о каждом компоненте, чтобы он мог отобразить заголовки и активную вкладку. Чтобы обеспечить эту координацию, не требуя дополнительного `TabSet` кода, компонент *может предоставить себя как каскадное значение* , которое затем будет использоваться компонентами- `Tab` потомками.

`TabSet`см

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

Компоненты- `Tab` потомки захватывают `TabSet` объект, содержащийся в виде каскадного `Tab` параметра, поэтому компоненты добавляются `TabSet` в и координаты, на которой активна вкладка.

`Tab`см

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Шаблоны Razor

Фрагменты визуализации можно определить с помощью синтаксиса шаблона Razor. Шаблоны Razor позволяют определить фрагмент пользовательского интерфейса и принять следующий формат:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

В следующем примере показано, как задавать `RenderFragment` значения `RenderFragment<T>` и, а также подготавливать шаблоны непосредственно в компоненте. Фрагменты рендеринга также могут передаваться в качестве аргументов в [шаблонные компоненты](#templated-components).

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Отображаемые выходные данные предыдущего кода:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>Логика Рендертрибуилдер вручную

`Microsoft.AspNetCore.Components.RenderTree`предоставляет методы для управления компонентами и элементами, включая создание компонентов вручную в C# коде.

> [!NOTE]
> `RenderTreeBuilder` Использование для создания компонентов является расширенным сценарием. Неправильно сформированный компонент (например, незакрытый тег разметки) может привести к неопределенному поведению.

Рассмотрим следующий `PetDetails` компонент, который можно вручную встроить в другой компонент:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    private string PetDetailsQuote { get; set; }
}
```

В следующем примере цикл в `CreateComponent` методе создает три `PetDetails` компонента. При вызове `RenderTreeBuilder` методов для создания компонентов (`OpenComponent` и `AddAttribute`) порядковые номера представляют собой номера строк исходного кода. Алгоритм разницы Блазор зависит от порядковых номеров, соответствующих разным строкам кода, а не отдельных вызовов вызова. При создании компонента с `RenderTreeBuilder` методами жестко указывайте аргументы для порядковых номеров. **Использование вычисления или счетчика для формирования порядкового номера может привести к снижению производительности.** Дополнительные сведения см. в разделе [порядковые номера относятся к разделу номера строк кода и порядок выполнения](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

`BuiltContent`см

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Порядковые номера связаны с номерами строк кода, а не с порядком выполнения

Файлы `.razor` блазор всегда компилируются. Это может быть отличным преимуществом `.razor` , поскольку этап компиляции можно использовать для вставки сведений, повышающих производительность приложения во время выполнения.

Ключевым примером этих улучшений являются *порядковые номера*. Порядковые номера указывают среде выполнения, какие выходные данные поступили из разных и упорядоченных строк кода. Среда выполнения использует эти сведения для создания эффективных различий дерева в линейное время, что является гораздо быстрее, чем обычно возможно для общего алгоритма различения дерева.

Рассмотрим следующий простой `.razor` файл:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

Предыдущий код компилируется примерно следующим образом:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Когда код выполняется в первый раз, если `someFlag` имеет `true`значение, построитель получает:

| Sequence | Тип      | Данные   |
| :------: | --------- | :----: |
| 0        | Текстовый узел | Первая  |
| 1        | Текстовый узел | Вторая |

Представьте, `someFlag` что `false`преобразуются, и разметка снова готовится к просмотру. На этот раз построитель получит:

| Sequence | Тип       | Данные   |
| :------: | ---------- | :----: |
| 1        | Текстовый узел  | Вторая |

Когда среда выполнения выполняет поиск различий, она видит, что элемент в `0` последовательности был удален, поэтому он создает следующий тривиальный *сценарий редактирования*:

* Удалите первый текстовый узел.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>Что становится неправильным, если вы создаете порядковые номера программным способом

Вместо этого Представьте себе, что вы написали следующую логику конструктора деревьев визуализации:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Теперь первые выходные данные:

| Sequence | Тип      | Данные   |
| :------: | --------- | :----: |
| 0        | Текстовый узел | Первая  |
| 1        | Текстовый узел | Вторая |

Этот результат идентичен предыдущему случаю, поэтому отрицательные проблемы не возникают. `someFlag`находится `false` во второй визуализации, а выходные данные:

| Sequence | Тип      | Данные   |
| :------: | --------- | ------ |
| 0        | Текстовый узел | Вторая |

На этот раз алгоритм diff видит, что были внесены *два* изменения, и алгоритм создает следующий сценарий редактирования:

* Измените значение первого текстового узла на `Second`.
* Удалите второй текстовый узел.

При формировании порядковых номеров теряются все полезные сведения о том `if/else` , где находятся ветви и циклы в исходном коде. Это приводит к удвоению в **два раза больше** , чем раньше.

Это тривиальный пример. В более реалистичных случаях со сложными и глубокими вложенными структурами, особенно с циклами, затраты на производительность более серьезны. Вместо того, чтобы сразу определять, какие блоки или ветви цикла были вставлены или удалены, алгоритм diff должен рекурсивно пребираться в деревьях отрисовки и, как правило, создает гораздо более длительные операции редактирования, так как он сообщает о том, как старую и новую структуры связь друг с другом.

#### <a name="guidance-and-conclusions"></a>Руководство и выводы

* Производительность приложения снижается, если порядковые номера создаются динамически.
* Платформа не может автоматически создавать собственные порядковые номера во время выполнения, поскольку необходимая информация не существует, если она не захвачена во время компиляции.
* Не записывайте длинные блоки логики, реализованной `RenderTreeBuilder` вручную. Предпочитать `.razor` файлы и разрешите компилятору работать с порядковыми номерами.
* Если порядковые номера задаются жестко, то для алгоритма diff требуется, чтобы только порядковые номера увеличиваются в значении. Начальное значение и зазоры несущественны. Один из этих вариантов — использовать номер строки кода в качестве порядкового номера или начать с нуля и увеличить на единицу или сотни (или любой другой интервал). 
* Блазор использует порядковые номера, а другие платформы пользовательского интерфейса для различения структуры не используют их. Сравнение выполняется гораздо быстрее, если используются порядковые номера, и блазор имеет преимущество этапа компиляции, который автоматически обрабатывает порядковые номера для разработчиков `.razor` файлов.