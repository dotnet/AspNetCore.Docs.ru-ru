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
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a><span data-ttu-id="f447e-103">Каскадные значения и параметры ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f447e-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="f447e-104">*Каскадные значения и параметры* обеспечивают удобный способ передачи данных по иерархии компонентов из компонента-предка в любое количество компонентов, расположенных ниже в иерархии.</span><span class="sxs-lookup"><span data-stu-id="f447e-104">*Cascading values and parameters* provide a convienent way to flow data down a component hierarchy from an ancestor component to any number of decendent components.</span></span> <span data-ttu-id="f447e-105">В отличие от [параметров компонента](xref:blazor/components/index#component-parameters), каскадные значения и параметры не требуют назначения атрибута для каждого компонента-потомка, где используются данные.</span><span class="sxs-lookup"><span data-stu-id="f447e-105">Unlike [Component parameters](xref:blazor/components/index#component-parameters), cascading values and parameters don't require an attribute assignment for each descendent component where the data is consumed.</span></span> <span data-ttu-id="f447e-106">Каскадные значения и параметры также позволяют компонентам согласовываться друг с другом в иерархии компонентов.</span><span class="sxs-lookup"><span data-stu-id="f447e-106">Cascading values and parameters also allow components to coordinate with each other across a component hierarchy.</span></span>

## <a name="cascadingvalue-component"></a><span data-ttu-id="f447e-107">`CascadingValue`</span><span class="sxs-lookup"><span data-stu-id="f447e-107">`CascadingValue` component</span></span>

<span data-ttu-id="f447e-108">Компонент-предок предоставляет каскадное значение с помощью компонента [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) платформы Blazor, который заключает поддерево иерархии компонентов и предоставляет одно значение для всех компонентов в его поддереве.</span><span class="sxs-lookup"><span data-stu-id="f447e-108">An ancestor component provides a cascading value using the Blazor framework's [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component, which wraps a subtree of a component hierarchy and supplies a single value to all of the components within its subtree.</span></span>

<span data-ttu-id="f447e-109">В следующем примере показана передача сведений о теме оформления в иерархии компонента макета для предоставления класса стиля CSS кнопкам в дочерних компонентах.</span><span class="sxs-lookup"><span data-stu-id="f447e-109">The following example demonstrates the flow of theme information down the component hierarchy of a layout component to provide a CSS style class to buttons in child components.</span></span>

<span data-ttu-id="f447e-110">Следующий класс C# `ThemeInfo` помещается в папку с именем `UIThemeClasses` и задает сведения о теме оформления.</span><span class="sxs-lookup"><span data-stu-id="f447e-110">The following `ThemeInfo` C# class is placed in a folder named `UIThemeClasses` and specifies the theme information.</span></span>

> [!NOTE]
> <span data-ttu-id="f447e-111">В примерах в этом разделе используется пространство имен приложения `BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="f447e-111">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="f447e-112">Экспериментируя с кодом в собственном примере приложения, измените пространство имен приложения на пространство имен своего примера приложения.</span><span class="sxs-lookup"><span data-stu-id="f447e-112">When experimenting with the code in your own sample app, change the app's namespace to your sample app's namespace.</span></span>

<span data-ttu-id="f447e-113">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="f447e-113">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
namespace BlazorSample.UIThemeClasses
{
    public class ThemeInfo
    {
        public string ButtonClass { get; set; }
    }
}
```

<span data-ttu-id="f447e-114">Следующий [компонент макета<xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> задает сведения о теме оформления (](xref:blazor/layouts)) как каскадное значение для всех компонентов, составляющих текст макета свойства `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="f447e-114">The following [layout component](xref:blazor/layouts) specifies theme information (`ThemeInfo`) as a cascading value for all components that make up the layout body of the <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property.</span></span> <span data-ttu-id="f447e-115">`ButtonClass` присваивается значение [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), которое является стилем кнопки начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="f447e-115">`ButtonClass` is assigned a value of [`btn-success`](https://getbootstrap.com/docs/5.0/components/buttons/), which is a Bootstrap button style.</span></span> <span data-ttu-id="f447e-116">Любой компонент-потомок в иерархии компонентов может использовать свойство `ButtonClass` через каскадное значение `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="f447e-116">Any descendent component in the component hierarchy can use the `ButtonClass` property through the `ThemeInfo` cascading value.</span></span>

<span data-ttu-id="f447e-117">`Shared/MainLayout.razor`:</span><span class="sxs-lookup"><span data-stu-id="f447e-117">`Shared/MainLayout.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,10-14,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/MainLayout.razor?highlight=2,9-13,17)]

::: moniker-end

## <a name="cascadingparameter-attribute"></a><span data-ttu-id="f447e-118">Атрибут `[CascadingParameter]`</span><span class="sxs-lookup"><span data-stu-id="f447e-118">`[CascadingParameter]` attribute</span></span>

<span data-ttu-id="f447e-119">Чтобы использовать каскадные значения, компоненты-потомки объявляют каскадные параметры с помощью [атрибута `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="f447e-119">To make use of cascading values, descendent components declare cascading parameters using the [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span> <span data-ttu-id="f447e-120">Каскадные значения привязаны к каскадным параметрам **по типу**.</span><span class="sxs-lookup"><span data-stu-id="f447e-120">Cascading values are bound to cascading parameters **by type**.</span></span> <span data-ttu-id="f447e-121">Каскадное применение нескольких значений одного и того же типа рассматривается в разделе [Каскадное применение нескольких значений](#cascade-multiple-values) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="f447e-121">Cascading multiple values of the same type is covered in the [Cascade multiple values](#cascade-multiple-values) section later in this article.</span></span>

<span data-ttu-id="f447e-122">Следующий компонент привязывает каскадное значение `ThemeInfo` к каскадному параметру, при необходимости используя то же имя `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="f447e-122">The following component binds the `ThemeInfo` cascading value to a cascading parameter, optionally using the same name of `ThemeInfo`.</span></span> <span data-ttu-id="f447e-123">Параметр используется для задания класса CSS для кнопки **`Increment Counter (Themed)`** .</span><span class="sxs-lookup"><span data-stu-id="f447e-123">The parameter is used to set the CSS class for the **`Increment Counter (Themed)`** button.</span></span>

<span data-ttu-id="f447e-124">`Pages/ThemedCounter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f447e-124">`Pages/ThemedCounter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/ThemedCounter.razor?highlight=2,15-17,23-24)]

::: moniker-end

## <a name="cascade-multiple-values"></a><span data-ttu-id="f447e-125">Каскадное применение нескольких значений</span><span class="sxs-lookup"><span data-stu-id="f447e-125">Cascade multiple values</span></span>

<span data-ttu-id="f447e-126">Чтобы выполнить каскадное применение нескольких значений одного типа в одном поддереве, укажите уникальную строку <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> для каждого компонента [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) и его соответствующих [атрибутов `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="f447e-126">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) component and their corresponding [`[CascadingParameter]` attributes](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute).</span></span>

<span data-ttu-id="f447e-127">В следующем примере два компонента [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) выполняют каскадное применение разных экземпляров `CascadingType`:</span><span class="sxs-lookup"><span data-stu-id="f447e-127">In the following example, two [`CascadingValue`](xref:Microsoft.AspNetCore.Components.CascadingValue%601) components cascade different instances of `CascadingType`:</span></span>

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

<span data-ttu-id="f447e-128">В компоненте-потомке каскадные параметры получают значения из соответствующих каскадных значений в компоненте-предке по <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span><span class="sxs-lookup"><span data-stu-id="f447e-128">In a descendant component, the cascaded parameters receive their cascaded values from the ancestor component by <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected CascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected CascadingType ChildCascadeParameter2 { get; set; }
}
```

## <a name="pass-data-across-a-component-hierarchy"></a><span data-ttu-id="f447e-129">Передача данных в иерархии компонентов</span><span class="sxs-lookup"><span data-stu-id="f447e-129">Pass data across a component hierarchy</span></span>

<span data-ttu-id="f447e-130">Каскадные параметры также позволяют компонентам передавать данные в рамках иерархии компонентов.</span><span class="sxs-lookup"><span data-stu-id="f447e-130">Cascading parameters also enable components to pass data across a component hierarchy.</span></span> <span data-ttu-id="f447e-131">Рассмотрим следующий пример набора вкладок пользовательского интерфейса, в котором компонент набора вкладок поддерживает ряд отдельных вкладок.</span><span class="sxs-lookup"><span data-stu-id="f447e-131">Consider the following UI tab set example, where a tab set component maintains a series of individual tabs.</span></span>

> [!NOTE]
> <span data-ttu-id="f447e-132">В примерах в этом разделе используется пространство имен приложения `BlazorSample`.</span><span class="sxs-lookup"><span data-stu-id="f447e-132">For the examples in this section, the app's namespace is `BlazorSample`.</span></span> <span data-ttu-id="f447e-133">Экспериментируя с кодом в собственном примере приложения, измените пространство имен приложения на пространство имен своего примера приложения.</span><span class="sxs-lookup"><span data-stu-id="f447e-133">When experimenting with the code in your own sample app, change the namespace to your sample app's namespace.</span></span>

<span data-ttu-id="f447e-134">Создайте интерфейс `ITab`, который реализуется вкладками в папке с именем `UIInterfaces`.</span><span class="sxs-lookup"><span data-stu-id="f447e-134">Create an `ITab` interface that tabs implement in a folder named `UIInterfaces`.</span></span>

<span data-ttu-id="f447e-135">`UIInterfaces/ITab.cs`:</span><span class="sxs-lookup"><span data-stu-id="f447e-135">`UIInterfaces/ITab.cs`:</span></span>

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

<span data-ttu-id="f447e-136">Следующий компонент `TabSet` содержит набор вкладок.</span><span class="sxs-lookup"><span data-stu-id="f447e-136">The following `TabSet` component maintains a set of tabs.</span></span> <span data-ttu-id="f447e-137">Компоненты набора вкладок `Tab`, которые создаются далее в этом разделе, предоставляют элементы списка (`<li>...</li>`) для списка (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="f447e-137">The tab set's `Tab` components, which are created later in this section, supply the list items (`<li>...</li>`) for the list (`<ul>...</ul>`).</span></span>

<span data-ttu-id="f447e-138">Дочерние компоненты `Tab` не передаются в `TabSet` в качестве параметров явным образом.</span><span class="sxs-lookup"><span data-stu-id="f447e-138">Child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="f447e-139">Вместо этого дочерние компоненты `Tab` являются частью дочернего содержимого `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="f447e-139">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="f447e-140">Однако `TabSet` по-прежнему требуется ссылка на каждый компонент `Tab`, чтобы визуализировать заголовки и активную вкладку. Чтобы обеспечить такую координацию без дополнительного кода, компонент `TabSet` *может предоставить себя в качестве каскадного значения*, которое затем используется компонентами-потомками `Tab`.</span><span class="sxs-lookup"><span data-stu-id="f447e-140">However, the `TabSet` still needs a reference each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="f447e-141">`Shared/TabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="f447e-141">`Shared/TabSet.razor`:</span></span>

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

<span data-ttu-id="f447e-142">Компоненты-потомки `Tab` захватывают объект, содержащий `TabSet`, в виде каскадного параметра.</span><span class="sxs-lookup"><span data-stu-id="f447e-142">Descendent `Tab` components capture the containing `TabSet` as a cascading parameter.</span></span> <span data-ttu-id="f447e-143">Компоненты `Tab` добавляют себя в `TabSet` и вместе задают активную вкладку.</span><span class="sxs-lookup"><span data-stu-id="f447e-143">The `Tab` components add themselves to the `TabSet` and coordinate to set the active tab.</span></span>

<span data-ttu-id="f447e-144">`Shared/Tab.razor`:</span><span class="sxs-lookup"><span data-stu-id="f447e-144">`Shared/Tab.razor`:</span></span>

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

<span data-ttu-id="f447e-145">Следующий компонент `ExampleTabSet` использует компонент `TabSet`, содержащий три компонента `Tab`.</span><span class="sxs-lookup"><span data-stu-id="f447e-145">The following `ExampleTabSet` component uses the `TabSet` component, which contains three `Tab` components.</span></span>

<span data-ttu-id="f447e-146">`Pages/ExampleTabSet.razor`:</span><span class="sxs-lookup"><span data-stu-id="f447e-146">`Pages/ExampleTabSet.razor`:</span></span>

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
