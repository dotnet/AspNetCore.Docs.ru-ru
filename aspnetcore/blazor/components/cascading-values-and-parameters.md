---
title: Каскадные значения и параметры ASP.NET Core Blazor
author: guardrex
description: Сведения о передаче данных из компонента предка в дочерние компоненты.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2021
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1fb9d75ca1613a7098840efd3ecb86ee90f4064c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280235"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>Каскадные значения и параметры ASP.NET Core Blazor

*Каскадные значения и параметры* обеспечивают удобный способ передачи данных по иерархии компонентов из компонента-предка в любое количество компонентов, расположенных ниже в иерархии. В отличие от [параметров компонента](xref:blazor/components/index#component-parameters), каскадные значения и параметры не требуют назначения атрибута для каждого компонента-потомка, где используются данные. Каскадные значения и параметры также позволяют компонентам согласовываться друг с другом в иерархии компонентов.

## <a name="cascadingvalue-component"></a>`CascadingValue`

Компонент-предок предоставляет каскадное значение с помощью компонента [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) платформы Blazor, который заключает поддерево иерархии компонентов и предоставляет одно значение для всех компонентов в его поддереве.

В следующем примере показана передача сведений о теме оформления в иерархии компонента макета для предоставления класса стиля CSS кнопкам в дочерних компонентах.

Следующий класс C# `ThemeInfo` помещается в папку с именем `UIThemeClasses` и задает сведения о теме оформления.

> [!NOTE]
> В примерах в этом разделе используется пространство имен приложения `BlazorSample`. Экспериментируя с кодом в собственном примере приложения, измените пространство имен приложения на пространство имен своего примера приложения.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

Следующий [компонент макета<xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> задает сведения о теме оформления (](xref:blazor/layouts)) как каскадное значение для всех компонентов, составляющих текст макета свойства `ThemeInfo`. `ButtonClass` присваивается значение [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), которое является стилем кнопки начальной загрузки. Любой компонент-потомок в иерархии компонентов может использовать свойство `ButtonClass` через каскадное значение `ThemeInfo`.

`Shared/MainLayout.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a>Атрибут `[CascadingParameter]`

Чтобы использовать каскадные значения, компоненты-потомки объявляют каскадные параметры с помощью [атрибута `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute). Каскадные значения привязаны к каскадным параметрам **по типу**. Каскадное применение нескольких значений одного и того же типа рассматривается в разделе [Каскадное применение нескольких значений](#cascade-multiple-values) далее в этой статье.

Следующий компонент привязывает каскадное значение `ThemeInfo` к каскадному параметру, при необходимости используя то же имя `ThemeInfo`. Параметр используется для задания класса CSS для кнопки **`Increment Counter (Themed)`** .

`Pages/ThemedCounter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a>Каскадное применение нескольких значений

Чтобы выполнить каскадное применение нескольких значений одного типа в одном поддереве, укажите уникальную строку <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> для каждого компонента [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) и его соответствующих [атрибутов `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).

В следующем примере два компонента [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) выполняют каскадное применение разных экземпляров `CascadingType`:

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private CascadingType parentCascadeParameter1;

    [Parameter]
    public CascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

В компоненте-потомке каскадные параметры получают значения из соответствующих каскадных значений в компоненте-предке по <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a>Передача данных в иерархии компонентов

Каскадные параметры также позволяют компонентам передавать данные в рамках иерархии компонентов. Рассмотрим следующий пример набора вкладок пользовательского интерфейса, в котором компонент набора вкладок поддерживает ряд отдельных вкладок.

> [!NOTE]
> В примерах в этом разделе используется пространство имен приложения `BlazorSample`. Экспериментируя с кодом в собственном примере приложения, измените пространство имен приложения на пространство имен своего примера приложения.

Создайте интерфейс `ITab`, который реализуется вкладками в папке с именем `UIInterfaces`.

`UIInterfaces/ITab.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample.UIInterfaces
{
    public interface ITab
    {
        RenderFragment ChildContent { get; }
    }
}
```

Следующий компонент `TabSet` содержит набор вкладок. Компоненты набора вкладок `Tab`, которые создаются далее в этом разделе, предоставляют элементы списка (`<li>...</li>`) для списка (`<ul>...</ul>`).

Дочерние компоненты `Tab` не передаются в `TabSet` в качестве параметров явным образом. Вместо этого дочерние компоненты `Tab` являются частью дочернего содержимого `TabSet`. Однако `TabSet` по-прежнему требуется ссылка на каждый компонент `Tab`, чтобы визуализировать заголовки и активную вкладку. Чтобы обеспечить такую координацию без дополнительного кода, компонент `TabSet` *может предоставить себя в качестве каскадного значения*, которое затем используется компонентами-потомками `Tab`.

`Shared/TabSet.razor`:

```razor
@using BlazorSample.UIInterfaces

<!-- Display the tab headers -->

<CascadingValue Value=this>
    <ul class="nav nav-tabs">
        @ChildContent
    </ul>
</CascadingValue>

<!-- Display body for only the active tab -->

<div class="nav-tabs-body p-4">
    @ActiveTab?.ChildContent
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public ITab ActiveTab { get; private set; }

    public void AddTab(ITab tab)
    {
        if (ActiveTab == null)
        {
            SetActiveTab(tab);
        }
    }

    public void SetActiveTab(ITab tab)
    {
        if (ActiveTab != tab)
        {
            ActiveTab = tab;
            StateHasChanged();
        }
    }
}
```

Компоненты-потомки `Tab` захватывают объект, содержащий `TabSet`, в виде каскадного параметра. Компоненты `Tab` добавляют себя в `TabSet` и вместе задают активную вкладку.

`Shared/Tab.razor`:

```razor
@using BlazorSample.UIInterfaces
@implements ITab

<li>
    <a @onclick="ActivateTab" class="nav-link @TitleCssClass" role="button">
        @Title
    </a>
</li>

@code {
    [CascadingParameter]
    public TabSet ContainerTabSet { get; set; }

    [Parameter]
    public string Title { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private string TitleCssClass => 
        ContainerTabSet.ActiveTab == this ? "active" : null;

    protected override void OnInitialized()
    {
        ContainerTabSet.AddTab(this);
    }

    private void ActivateTab()
    {
        ContainerTabSet.SetActiveTab(this);
    }
}
```

Следующий компонент `ExampleTabSet` использует компонент `TabSet`, содержащий три компонента `Tab`.

`Pages/ExampleTabSet.razor`:

```razor
@page "/example-tab-set"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>

    <Tab Title="Second tab">
        <h4>Hello from the second tab!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```
