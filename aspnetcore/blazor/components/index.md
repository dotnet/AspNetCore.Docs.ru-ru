---
title: Создание и использование компонентов Razor ASP.NET Core
author: guardrex
description: Сведения о том, как создавать и использовать компоненты Razor, включая способы привязки к данным, обработки событий и управления жизненным циклом компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: 111512916cb7f0a4fc1f17648e2f9c69e366dff3
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107055"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Создание и использование компонентов Razor ASP.NET Core

Авторы: [Люк Латэм (Luke Latham)](https://github.com/guardrex), [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27), [Скотт Эдди (Scott Addie)](https://github.com/scottaddie) и [Тобиас Бартщ (Tobias Bartsch)](https://www.aveo-solutions.com/)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

Приложения Blazor создаются с использованием *компонентов*. Компонент — это автономный блок пользовательского интерфейса, такой как страница, диалоговое окно или форма. Компонент включает разметку HTML и логику обработки, необходимую для внедрения данных или реагирования на события пользовательского интерфейса. Компоненты являются гибкими и облегченными. Их можно вкладывать, использовать повторно и сразу в нескольких проектах.

## <a name="component-classes"></a>Классы компонентов

Компоненты реализуются в файлах компонентов [Razor](xref:mvc/views/razor) (`.razor`) с помощью комбинации разметки HTML и C#. Компонент в Blazor формально называется *компонентом Razor* .

### <a name="razor-syntax"></a>Синтаксис Razor

В компонентах Razor приложений Blazor часто используется синтаксис Razor. Если вы не знакомы с языком разметки Razor, рекомендуем сначала ознакомиться со [справочником по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor).

При доступе к содержимому с синтаксисом Razor обратите особое внимание на следующее:

* [Директивы](xref:mvc/views/razor#directives) — это зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.
* [Атрибуты директивы](xref:mvc/views/razor#directive-attributes) — зарезервированные ключевые слова с префиксом `@`, которые обычно меняют то, как разметка компонента анализируется и функционирует.

### <a name="names"></a>Имена

Имя компонента должно начинаться с заглавной буквы. Например, `MyCoolComponent.razor` допустимо использовать, а `myCoolComponent.razor` нет.

### <a name="routing"></a>Маршрутизация

Маршрутизация в Blazor достигается путем предоставления шаблона маршрута каждому доступному компоненту в приложении. При компиляции файла Razor с директивой [`@page`][9] созданному классу предоставляется атрибут <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, указывающий шаблон маршрута. Во время выполнения маршрутизатор ищет классы компонентов с <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> и отображает любой компонент, шаблон маршрута которого соответствует запрошенному URL-адресу. Для получения дополнительной информации см. <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>разметку

Пользовательский интерфейс для компонента определяется с помощью HTML. Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием *Razor* . Во время компиляции приложения разметка HTML и логика отрисовки C# преобразуются в класс компонента. Имя создаваемого класса соответствует имени файла.

Элементы класса компонента определяются в блоке [`@code`][1]. В блоке [`@code`][1] указываются состояние (свойства, поля) компонента и методы для обработки событий или определения другой логики компонента. Допускается использование нескольких блоков [`@code`][1].

Элементы компонента можно использовать как часть логики отрисовки компонента с использованием выражений C#, начинающихся с `@`. Например, поле C# отрисовывается путем добавления `@` к имени поля. В следующем примере вычисляется и отрисовывается следующее:

* `headingFontStyle` в значении свойства CSS для `font-style`.
* `headingText` в содержимом элемента `<h1>`.

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

После первоначальной отрисовки компонента он повторно создает дерево отрисовки в ответ на события. Затем Blazor сравнивает новое и прежнее дерево отрисовки и применяет все изменения в модели DOM браузера. Дополнительные сведения приведены в статье <xref:blazor/components/rendering>.

Компоненты являются обычными классами C# и могут размещаться в любом месте внутри проекта. Компоненты, создающие веб-страницы, обычно находятся в папке `Pages`. Компоненты, не являющиеся страницами, часто находятся в папке `Shared` или пользовательской папке, добавленной в проект.

### <a name="namespaces"></a>Пространства имен

Как правило, пространство имен компонента является производным от корневого пространства имен приложения и расположения компонента (папки) в приложении. Если пространством имен корня приложения является `BlazorSample`, а компонент `Counter` находится в папке `Pages`:

* Пространством имен компонента `Counter` является `BlazorSample.Pages`.
* Полным именем компонента является `BlazorSample.Pages.Counter`.

При использовании пользовательских папок, содержащих компоненты, добавьте директиву [`@using`][2] в родительский компонент или в файл `_Imports.razor` приложения. В следующем примере становятся доступными компоненты в папке `Components`.

```razor
@using BlazorSample.Components
```

На компоненты также можно ссылаться с помощью полных имен, для чего не требуется директива [`@using`][2]:

```razor
<BlazorSample.Components.MyComponent />
```

Пространство имен компонента, созданного с помощью Razor, основано на следующем (в порядке приоритета).

* Назначение [`@namespace`][8] в разметке (`@namespace BlazorSample.MyNamespace`) файла Razor (`.razor`).
* `RootNamespace` проекта в файле проекта (`<RootNamespace>BlazorSample</RootNamespace>`).
* Имя проекта, полученное из имени файла проекта (`.csproj`), и путь из корневого каталога проекта к компоненту. Например, платформа разрешает `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) в пространство имен `BlazorSample.Pages`. Компоненты соответствуют правилам привязки имен C#. Для компонента `Index` в этом примере компонентами в области действия являются все компоненты:
  * в этой же папке `Pages`;
  * в корневой папке проекта, которая не задает другое пространство имен явным образом.

> [!NOTE]
> Квалификация `global::` не поддерживается.
>
> Импорт компонентов с инструкциями [`using`](/dotnet/csharp/language-reference/keywords/using-statement), содержащими псевдонимы, (например, `@using Foo = Bar`) не поддерживается.
>
> Частично определенные имена не поддерживаются. Например, добавление `@using BlazorSample` и ссылка на компонент `NavMenu` (`NavMenu.razor`) с помощью `<Shared.NavMenu></Shared.NavMenu>` не поддерживаются.

### <a name="partial-class-support"></a>Поддержка разделяемых классов

Компоненты Razor создаются как разделяемые классы. Создавать компоненты Razor можно одним из следующих способов.

* Код C# определяется в блоке [`@code`][1] с разметкой HTML и кодом Razor в одном файле. Шаблоны Blazor определяют свои компоненты Razor с помощью этого подхода.
* Код C# помещается в файл кода программной части, определенный как разделяемый класс.

В следующем примере показан компонент `Counter` по умолчанию с блоком [`@code`][1] в приложении, созданном из шаблона Blazor. Разметка HTML, код Razor и код C# находятся в одном файле.

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Компонент `Counter` также можно создать, используя файл кода программной части с разделяемым классом:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

При необходимости добавьте в файл разделяемого класса нужные пространства имен. К типичным пространствам имен, используемым компонентами Razor, относятся следующие.

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> Директивы [`@using`][2] в файле `_Imports.razor` применяются только к файлам Razor (`.razor`), но не к файлам C# (`.cs`).

### <a name="specify-a-base-class"></a>Указание базового класса

Директиву [`@inherits`][6] можно использовать для указания базового класса для компонента. В следующем примере показано, как компонент может наследовать базовый класс `BlazorRocksBase`, чтобы предоставить свойства и методы компонента. Базовый класс должен быть производным от <xref:Microsoft.AspNetCore.Components.ComponentBase>.

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a>Использование компонентов

Компоненты могут включать другие компоненты, объявляя их с помощью синтаксиса HTML-элементов. Разметка для использования компонента выглядит как тег HTML с именем, соответствующем типу компонента.

Следующая разметка в `Pages/Index.razor` преобразовывает экземпляр `HeadingComponent` для просмотра.

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Если компонент содержит HTML-элемент с первой заглавной буквой, который не соответствует имени компонента, выдается предупреждение о том, что элемент имеет непредвиденное имя. Добавление директивы [`@using`][2] для пространства имен компонента делает компонент доступным, что позволяет устранить это предупреждение.

## <a name="parameters"></a>Параметры

### <a name="route-parameters"></a>Параметры маршрута

Компоненты могут принимать параметры маршрута из шаблона маршрута, указанного в директиве [`@page`][9]. Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента.

::: moniker range=">= aspnetcore-5.0"

Поддерживаются необязательные параметры. В следующем примере необязательный параметр `text` назначает значение сегмента маршрута свойству `Text` компонента. Если сегмента нет, для `Text` устанавливается значение `fantastic`.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

Необязательные параметры не поддерживаются, поэтому в предыдущем примере применяются две директивы [`@page`][9]. Первая позволяет переходить к компоненту без параметра. Вторая директива [`@page`][9] принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.

::: moniker-end

Сведения об универсальных параметрах маршрута (`{*pageRoute}`), которые захватывают пути в нескольких папках, см. в разделе <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="component-parameters"></a>Параметры компонентов

Компоненты могут иметь *параметры*, определяемые с помощью открытых простых или сложных свойств в классе компонента с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute). Используйте атрибуты, чтобы указать аргументы для компонента в разметке.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Параметрам компонента может быть присвоено значение по умолчанию:

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

В следующем примере из примера приложения `ParentComponent` задает значение свойства `Title` для `ChildComponent`.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

В соответствии с соглашением значение атрибута, состоящее из кода C#, назначается параметру с помощью [зарезервированного символа Razor `@`](xref:mvc/views/razor#razor-syntax):

* Родительское поле или свойство `Title="@{FIELD OR PROPERTY}`, где заполнитель `{FIELD OR PROPERTY}` является полем C# или свойством родительского компонента.
* Результат метода `Title="@{METHOD}"`, где заполнитель `{METHOD}` является методом C# родительского компонента.
* [Неявное или явное выражение](xref:mvc/views/razor#implicit-razor-expressions) `Title="@({EXPRESSION})"`, где заполнитель `{EXPRESSION}` является выражением C#.
  
Дополнительные сведения см. в [справочнике по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor).

> [!WARNING]
> Не создавайте компоненты, записывающие их в собственные *параметры компонентов*, — используйте вместо этого закрытое поле. Дополнительные сведения см. в разделе [Перезаписанные параметры](#overwritten-parameters).

## <a name="child-content"></a>Дочернее содержимое

Компоненты могут задавать содержимое другого компонента. Назначение компонента задает содержимое между тегами, указывающими принимающий компонент.

В следующем примере `ChildComponent` имеет свойство `ChildContent`, представляющее <xref:Microsoft.AspNetCore.Components.RenderFragment>, который представляет сегмент пользовательского интерфейса для отрисовки. Значение `ChildContent` находится в том месте разметки компонента, где должно быть визуализировано содержимое. Значение `ChildContent` принимается от родительского компонента и визуализируется внутри `panel-body` панели начальной загрузки.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Свойству, принимающему содержимое <xref:Microsoft.AspNetCore.Components.RenderFragment>, по соглашению необходимо присвоить имя `ChildContent`.

`ParentComponent` в примере приложения может предоставить содержимое для отрисовки `ChildComponent`, заключив содержимое в теги `<ChildComponent>`.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для компонентов рендеринга в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента используется переменная цикла приращения:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

Сведения о том, как использовать <xref:Microsoft.AspNetCore.Components.RenderFragment> в качестве шаблона для пользовательского интерфейса компонента Razor, см. в следующих статьях:

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Сплаттинг атрибутов и произвольные параметры

Компоненты могут записывать и визуализировать дополнительные атрибуты в дополнение к объявленным параметрам компонента. Можно записать дополнительные атрибуты в словарь, а затем выполнить *сплаттинг* для элемента при подготовке отрисовки компонента с помощью директивы [`@attributes`][3] Razor. Этот сценарий полезен при определении компонента, который создает элемент разметки, поддерживающий разнообразные настройки. Например, может оказаться утомительным по отдельности определять атрибуты для `<input>`, поддерживающего много параметров.

В следующем примере первый элемент `<input>` (`id="useIndividualParams"`) использует отдельные параметры компонента, а второй элемент `<input>` (`id="useAttributesDict"`) использует сплаттинг атрибутов:

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Тип параметра должен реализовывать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>` с ключами строки.

При использовании обоих подходов получаются идентичные отрисованные элементы `<input>`:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Чтобы принять произвольные атрибуты, определите параметр компонента с помощью атрибута [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) со свойством <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, имеющим значение `true`:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Свойство <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> в [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) позволяет параметру соответствовать всем атрибутам, которые не соответствуют никакому другому параметру. Компонент может определять только один параметр с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>. Тип свойства, используемый с <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>, должен быть назначаемым из `Dictionary<string, object>` с ключами строки. В этом сценарии также можно использовать `IEnumerable<KeyValuePair<string, object>>` или `IReadOnlyDictionary<string, object>`.

Расположение [`@attributes`][3] относительно положения атрибутов элемента имеет значение. Когда выполняется сплаттинг [`@attributes`][3] для элемента, атрибуты обрабатываются справа налево (от последнего к первому). Рассмотрим следующий пример компонента, использующего компонент `Child`:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Атрибут `extra` компонента `Child` стоит справа от [`@attributes`][3]. `<div>`, визуализируемый компонентом `Parent`, содержит `extra="5"` при передаче через дополнительный атрибут, так как атрибуты обрабатываются справа налево (от последнего к первому):

```html
<div extra="5" />
```

В следующем примере порядок `extra` и [`@attributes`][3] в `<div>` компонента `Child` изменен на противоположный:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Визуализируемый `<div>` в компоненте `Parent` содержит `extra="10"` при передаче через дополнительный атрибут:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Запись ссылок на компоненты

Ссылки на компоненты позволяют ссылаться на экземпляр компонента, чтобы можно было выполнять команды для этого экземпляра, например `Show` или `Reset`. Чтобы записать ссылку на компонент, сделайте следующее:

* Добавьте к дочернему компоненту атрибут [`@ref`][4].
* Определите поле с тем же типом, что и у дочернего компонента.

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

При отрисовке компонента поле `loginDialog` заполняется экземпляром дочернего компонента `CustomLoginDialog`. Затем можно вызывать методы .NET в экземпляре компонента.

> [!IMPORTANT]
> Переменная `loginDialog` заполняется только после отрисовки компонента, а ее выходные данные включают элемент `MyLoginDialog`. Пока компонент не будет преобразован для просмотра, ссылка на него не используется.
>
> Для управления ссылками на компоненты после завершения отрисовки компонента используйте [методы `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).
>
> Чтобы использовать ссылочную переменную с обработчиком событий, используйте лямбда-выражение или назначьте делегат обработчика событий в [методах `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render). Это гарантирует, что ссылочная переменная будет назначена до назначения обработчика событий.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Сведения о ссылках на компоненты в цикле см. в разделе [Получение ссылок на несколько схожих дочерних компонентов (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

При записи ссылок на компоненты используется аналогичный синтаксис для [записи ссылок на элементы](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), это не функция взаимодействия JavaScript. Ссылки на компоненты не передаются в код JavaScript. Они используются только в коде .NET.

> [!NOTE]
> **Не** используйте ссылки на компоненты для изменения состояния дочерних компонентов. Вместо этого используйте обычные декларативные параметры для передачи данных дочерним компонентам. Использование обычных декларативных параметров приводит к тому, что дочерние компоненты автоматически визуализируются в нужное время.

## <a name="synchronization-context"></a>Контекст синхронизации

Blazor использует контекст синхронизации (<xref:System.Threading.SynchronizationContext>) для принудительного использования одного логического потока выполнения. [Методы жизненного цикла ](xref:blazor/components/lifecycle) компонента и все обратные вызовы событий, сделанные Blazor, выполняются в этом контексте синхронизации.

Контекст синхронизации Blazor Server пытается эмулировать однопоточную среду таким образом, чтобы она точно соответствовала модели WebAssembly в браузере, которая является однопоточной. В любой момент времени работа выполняется только в одном потоке, что создает впечатление единого логического потока. Две операции не могут выполняться одновременно.

### <a name="avoid-thread-blocking-calls"></a>Избегайте блокирующих вызовов

Как правило, не следует вызывать следующие методы. Следующие методы блокируют поток и, таким образом, блокируют возобновление работы приложения до тех пор, пока не завершится базовый <xref:System.Threading.Tasks.Task>:

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Внешний вызов методов компонента для изменения состояния

Если компонент нужно изменить на основе внешнего события, такого как таймер или другие уведомления, используйте метод `InvokeAsync`, который выполняет отправку в контекст синхронизации Blazor. Например, рассмотрим *службу уведомителя*, которая может уведомлять любой компонент, ожидающий передачи данных, об измененном состоянии:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Зарегистрируйте `NotifierService`.

* В Blazor WebAssembly зарегистрируйте службу как отдельную (singleton) в `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* В Blazor Server зарегистрируйте службу с заданной областью (scoped) в `Startup.ConfigureServices`:

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Используйте `NotifierService` для изменения компонента:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

В предшествующем примере:

* `NotifierService` вызывает метод `OnNotify` компонента вне контекста синхронизации Blazor. `InvokeAsync` используется для переключения на подходящий контекст и постановки отрисовки в очередь. Для получения дополнительной информации см. <xref:blazor/components/rendering>.
* Компонент реализует <xref:System.IDisposable>, а делегат `OnNotify` не подписан в методе `Dispose`, который вызывается платформой при удалении компонента. Для получения дополнительной информации см. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Использование \@key для управления сохранением элементов и компонентов

При отрисовке списка элементов или компонентов и последующем изменении элементов или компонентов алгоритм сравнения Blazor должен решить, какие из предыдущих элементов или компонентов можно оставить и как следует сопоставить с ними объекты модели. Обычно этот процесс выполняется автоматически, и его можно игнорировать, но в некоторых случаях может потребоваться управлять данным процессом.

Рассмотрим следующий пример.

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Содержимое коллекции `People` может изменяться при вставке, удалении или повторном упорядочении записей. Когда компонент отрисовывается повторно, компонент `<DetailsEditor>` может измениться на получение других значений параметра `Details`. Это может усложнить повторную отрисовку. В некоторых случаях повторная отрисовка может привести к появлению заметных различий в поведении, таких как потеря фокуса элемента.

Процесс сопоставления можно контролировать с помощью атрибута директивы [`@key`][5]. [`@key`][5] гарантирует, что алгоритм сравнения сохранит элементы или компоненты на основе значения ключа:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

При изменении коллекции `People` алгоритм сравнения сохраняет связь между экземплярами `<DetailsEditor>` и экземплярами `person`:

* Если `Person` удаляется из списка `People`, то из пользовательского интерфейса удаляется только соответствующий экземпляр `<DetailsEditor>`. Другие экземпляры остаются без изменений.
* Если в какой-либо позиции в списке вставляется `Person`, то в соответствующей позиции вставляется один новый экземпляр `<DetailsEditor>`. Другие экземпляры остаются без изменений.
* При переупорядочении записей `Person` соответствующие экземпляры `<DetailsEditor>` сохраняются и переупорядочиваются в пользовательском интерфейсе.

В некоторых сценариях использование [`@key`][5] уменьшает сложность повторной отрисовки и предотвращает потенциальные проблемы с изменяющимися элементами модели DOM с отслеживанием состояния, например положением фокуса.

> [!IMPORTANT]
> Ключи являются локальными для каждого компонента или элемента контейнера. Ключи не сравниваются глобально по всему документу.

### <a name="when-to-use-key"></a>Условия для использования \@key

Как правило, [`@key`][5] имеет смысл использовать при отрисовке списка (например, в блоке [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)) и при наличии подходящего значения для определения [`@key`][5].

Можно также использовать [`@key`][5], чтобы запретить Blazor сохранять поддерево элементов или компонентов при изменении объекта:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Если `@currentPerson` меняется, директива атрибута [`@key`][5] принуждает Blazor отменить весь блок элемента `<div>` с потомками и перестроить поддерево в пользовательском интерфейсе с использованием новых элементов и компонентов. Это может быть полезно, если нужно гарантировать, что при изменении `@currentPerson` состояние пользовательского интерфейса не сохраняется.

### <a name="when-not-to-use-key"></a>Условия для отказа от использования \@key

Сравнение с использованием [`@key`][5] подразумевает определенное снижение производительности. Это снижение производительности незначительно, но указывать [`@key`][5] следует только в том случае, если управление правилами сохранения элементов или компонентов выгодно для приложения.

Даже если [`@key`][5] не используется, Blazor сохраняет экземпляры дочерних элементов и компонентов в максимально возможной степени. Единственным преимуществом использования [`@key`][5] является контроль над тем, *как* экземпляры модели сопоставляются с сохраненными экземплярами компонентов, вместо выбора сопоставления с помощью алгоритма сравнения.

### <a name="what-values-to-use-for-key"></a>Значения, которые следует использовать для \@key

Как правило, для [`@key`][5] имеет смысл указать значение одного из следующих видов:

* Экземпляры объектов модели (например, экземпляр `Person`, как в предыдущем примере). Это гарантирует сохранение на основе равенства ссылок на объекты.
* Уникальные идентификаторы (например, значения первичного ключа типа `int`, `string` или `Guid`).

Убедитесь, что значения, используемые для [`@key`][5], не конфликтуют. Если в одном родительском элементе обнаруживаются конфликтующие значения, Blazor выдает исключение, поскольку не может детерминированно сопоставлять старые элементы или компоненты с новыми. Используйте только уникальные значения, такие как экземпляры объекта или значения первичного ключа.

## <a name="overwritten-parameters"></a>Перезаписанные параметры

Платформа Blazor обычно позволяет безопасно назначить параметр с родительского компонента на дочерний:

* Параметры не перезаписываются неожиданно.
* Побочные эффекты сводятся к минимуму. Например, дополнительные отрисовки избегаются, так как они могут создавать бесконечные циклы отрисовки.

Дочерний компонент получает новые значения параметров, которые могут перезаписать существующие значения при повторной отрисовке родительского компонента. Случайно перезаписанные значения параметров в дочернем компоненте часто возникают при разработке компонента с одним или несколькими параметрами, привязанными к данным. А также, если разработчик выполняет запись данных непосредственно в параметр в дочернем элементе:

* Дочерний компонент отрисовывается с одним или несколькими значениями параметров из родительского компонента.
* Дочерний компонент выполняет запись непосредственно в значение параметра.
* Родительский компонент отрисовывается повторно и перезаписывает значение параметра дочернего элемента.

Возможность перезаписи значений параметров также распространяется и на методы задания свойств дочернего компонента.

**В наших общих рекомендациях не описана процедура создания компонентов, которые непосредственно записываются в собственные параметры.**

Рассмотрим следующий компонент `Expander` с ошибкой, который

* преобразует дочернее содержимое;
* переключает отображение дочернего содержимого с помощью параметра компонента (`Expanded`).
* Компонент выполняет запись непосредственно в параметр `Expanded`, который демонстрирует проблему с перезаписанными параметрами. Его следует избегать.

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Компонент `Expander` добавляется в родительский компонент, который может вызывать <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

Изначально компоненты `Expander` работают независимо, когда их свойства `Expanded` переключаются. Дочерние компоненты сохраняют свои состояния, как и ожидалось. Когда в родительском элементе вызывается <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, параметр `Expanded` первого дочернего компонента сбрасывается обратно к первоначальному значению (`true`). Значение `Expanded` второго компонента `Expander` не сбрасывается, так как во втором компоненте не отображается дочернее содержимое.

Чтобы сохранить состояние в предыдущем сценарии, используйте *закрытое поле* в компоненте `Expander`, чтобы сохранить состояние переключения.

Следующий измененный компонент `Expander`:

* Принимает значение параметра компонента `Expanded` из родительского элемента.
* Присваивает значение параметра компонента *закрытому полю* (`expanded`) при [событии OnInitialized](xref:blazor/components/lifecycle#component-initialization-methods).
* Использует закрытое поле для поддержания внутреннего состояния переключения, в котором описывается, как избегать непосредственной записи в параметре.

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

Дополнительные сведения см. в статье [Ошибка двусторонней привязки Blazor (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599). 

## <a name="apply-an-attribute"></a>Применение атрибута

Атрибуты можно применять к компонентам Razor с помощью директивы [`@attribute`][7]. В следующем примере атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) применяется к классу компонентов:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Условные атрибуты элемента HTML

Атрибуты элемента HTML условно визуализируются в зависимости от значения .NET. Если значение равно `false` или `null`, то атрибут не визуализируется. Если значение равно `true`, атрибут визуализируется в свернутом виде.

В следующем примере `IsCompleted` определяет, визуализируется ли `checked` в разметке элемента:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Если `IsCompleted` имеет значение `true`, флажок визуализируется следующим образом:

```html
<input type="checkbox" checked />
```

Если `IsCompleted` имеет значение `false`, флажок визуализируется следующим образом:

```html
<input type="checkbox" />
```

Дополнительные сведения см. в [справочнике по синтаксису Razor для ASP.NET Core](xref:mvc/views/razor).

> [!WARNING]
> Некоторые атрибуты HTML, такие как [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), работают неправильно, если типом .NET является `bool`. В этих случаях используйте тип `string` вместо `bool`.

## <a name="raw-html"></a>Необработанный HTML-код

Строки обычно визуализируются с помощью текстовых узлов модели DOM, что означает, что любая разметка, которую они могут содержать, игнорируется и обрабатывается как текстовый литерал. Для отрисовки необработанного HTML-кода заключите HTML-содержимое в значение `MarkupString`. Это значение анализируется как HTML или SVG и вставляется в модель DOM.

> [!WARNING]
> Отрисовка необработанного HTML-кода, созданного из любого ненадежного источника, является **угрозой безопасности**, и ее следует избегать!

В следующем примере показано использование типа `MarkupString` для добавления блока статического HTML-содержимого в визуализируемые выходные данные компонента:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Шаблоны Razor

Фрагменты отрисовки можно определить с помощью синтаксиса шаблонов Razor. Шаблоны Razor позволяют определить фрагмент кода пользовательского интерфейса и подразумевают следующий формат.

```razor
@<{HTML tag}>...</{HTML tag}>
```

В следующем примере показано, как указать значения <xref:Microsoft.AspNetCore.Components.RenderFragment> и <xref:Microsoft.AspNetCore.Components.RenderFragment%601> и визуализировать шаблоны непосредственно в компоненте. Фрагменты отрисовки также могут передаваться в качестве аргументов в [шаблонные компоненты](xref:blazor/components/templated-components).

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Визуализированные выходные данные предыдущего кода:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>Статические ресурсы.

Blazor соответствует соглашению для приложений ASP.NET Core о размещении статических ресурсов в [папке `web root (wwwroot)`](xref:fundamentals/index#web-root) проекта.

Используйте базовый относительный путь (`/`) для ссылки на корневой веб-каталог статического ресурса. В следующем примере `logo.png` физически находится в папке `{PROJECT ROOT}/wwwroot/images`.

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Компоненты Razor **не** поддерживают нотацию тильды с косой чертой (`~/`).

Сведения о настройке базового пути приложения см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Вспомогательные функции тегов в компонентах не поддерживаются

[`Tag Helpers`Razor не поддерживаются в компонентах ](xref:mvc/views/tag-helpers/intro) (файлы с расширением `.razor`). Чтобы обеспечить функциональные возможности, аналогичные вспомогательным функциям тегов, в Blazor, создайте компонент с теми же функциональными возможностями, что и вспомогательная функция тега, и используйте его вместо нее.

## <a name="scalable-vector-graphics-svg-images"></a>Изображения SVG

Так как Blazor выполняет рендеринг HTML-кода, поддерживаемые браузером изображения, включая изображения SVG (`.svg`), поддерживаются при использовании тега `<img>`.

```html
<img alt="Example image" src="some-image.svg" />
```

Аналогичным образом изображения SVG поддерживаются в правилах CSS файла таблицы стилей (`.css`).

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Однако встроенная разметка SVG не поддерживается во всех сценариях. Если поместить тег `<svg>` непосредственно в файл компонента (`.razor`), базовая отрисовка изображений будет доступной, но многие расширенные сценарии пока не поддерживаются. Например, теги `<use>` сейчас не учитываются, а с некоторыми тегами SVG невозможно использовать [`@bind`][10]. Дополнительные сведения см. в [справке по SVG в Blazor (dotnet/aspnetcore#18271)](https://github.com/dotnet/aspnetcore/issues/18271).

## <a name="whitespace-rendering-behavior"></a>Поведение при отрисовке пробелов

::: moniker range=">= aspnetcore-5.0"

Если директива [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) не используется со значением `true`, лишние пробелы будут удаляться по умолчанию, если:

* Они находятся в начале или конце элемента.
* Они находятся в начале или конце параметра `RenderFragment`. Например, когда дочернее содержимое передается другому компоненту.
* Они находятся в начале или конце блока кода C#, например `@if` и `@foreach`.

При использовании правила CSS, такого как `white-space: pre`, удаление пробелов может повлиять на отображаемые выходные данные. Чтобы отключить эту оптимизацию производительности и сохранить пробелы, выполните одно из следующих действий.

* Добавьте директиву `@preservewhitespace true` в начало  `.razor`-файла, чтобы применить предпочтение к конкретному компоненту.
* Добавьте директиву `@preservewhitespace true` внутрь файла `_Imports.razor`, чтобы применить предпочтение ко всему подкаталогу или проекту целиком.

В большинстве случаев никаких действий не требуется, так как приложения, как правило, продолжают работать в обычном режиме (но быстрее). Если удаление пробелов приводит к возникновению проблем в конкретном компоненте, используйте `@preservewhitespace true` в таком компоненте, чтобы отключить эту оптимизацию.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Пробел сохраняется в исходном коде компонента. Текст, состоящий только из пробелов, отображается в модели DOM браузера даже при отсутствии визуального эффекта.

Рассмотрим следующий код компонента Razor:

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

В предыдущем примере отображается следующий ненужный пробел:

* за пределами блока кода `@foreach`;
* вокруг элемента `<li>`;
* вокруг выходных данных `@item.Text`.

Для списка, содержащего 100 элементов, создается 402 области пробелов. При этом ни один из дополнительных пробелов не будет визуально влиять на отображаемые выходные данные.

При отображении статического кода HTML для компонентов пробелы внутри тега не сохраняются. Например, просмотрите исходный код следующего компонента в отображаемых выходных данных:

```razor
<img     alt="My image"   src="img.png"     />
```

Пробелы из предыдущей разметки Razor не сохраняются:

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/server/threat-mitigation>. Содержит рекомендации по созданию приложений Blazor Server, которые должны соперничать в условиях нехватки ресурсов.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "Справочник по синтаксису Razor для ASP.NET Core"
[2]: <xref:mvc/views/razor#using> "Справочник по синтаксису Razor для ASP.NET Core"
[3]: <xref:mvc/views/razor#attributes> "Справочник по синтаксису Razor для ASP.NET Core"
[4]: <xref:mvc/views/razor#ref> "Справочник по синтаксису Razor для ASP.NET Core"
[5]: <xref:mvc/views/razor#key> "Справочник по синтаксису Razor для ASP.NET Core"
[6]: <xref:mvc/views/razor#inherits> "Справочник по синтаксису Razor для ASP.NET Core"
[7]: <xref:mvc/views/razor#attribute> "Справочник по синтаксису Razor для ASP.NET Core"
[8]: <xref:mvc/views/razor#namespace> "Справочник по синтаксису Razor для ASP.NET Core"
[9]: <xref:mvc/views/razor#page> "Справочник по синтаксису Razor для ASP.NET Core"
[10]: <xref:mvc/views/razor#bind> "Справочник по синтаксису Razor для ASP.NET Core"
