---
title: Жизненный цикл ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 6e9d2c3180fb9e4c3e5ccc0b6d8e17183f78d698
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109849"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="0f447-103">Жизненный цикл ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0f447-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="0f447-104">Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="0f447-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="0f447-105">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="0f447-106">На приведенных ниже схемах показан жизненный цикл Blazor.</span><span class="sxs-lookup"><span data-stu-id="0f447-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="0f447-107">Определение методов жизненного цикла с примерами приводится в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="0f447-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="0f447-108">События жизненного цикла компонента</span><span class="sxs-lookup"><span data-stu-id="0f447-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="0f447-109">Если компонент отрисовывается в первый раз по запросу:</span><span class="sxs-lookup"><span data-stu-id="0f447-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="0f447-110">Создайте экземпляр компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-110">Create the component's instance.</span></span>
   * <span data-ttu-id="0f447-111">Выполните внедрение свойств.</span><span class="sxs-lookup"><span data-stu-id="0f447-111">Perform property injection.</span></span> <span data-ttu-id="0f447-112">Выполните [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="0f447-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="0f447-113">Вызовите [`OnInitialized{Async}`](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="0f447-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="0f447-114">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="0f447-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="0f447-115">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="0f447-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="0f447-116">Вызовите метод [`OnParametersSet{Async}`](#after-parameters-are-set) и выполните отрисовку компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="0f447-117">Если метод `OnParametersSetAsync` возвращает значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="0f447-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![События жизненного цикла компонента Razor в Blazor](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="0f447-119">Обработка событий модели DOM</span><span class="sxs-lookup"><span data-stu-id="0f447-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="0f447-120">Выполняется обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="0f447-120">The event handler is run.</span></span>
1. <span data-ttu-id="0f447-121">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="0f447-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="0f447-122">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="0f447-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Обработка событий модели DOM](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="0f447-124">Жизненный цикл `Render`</span><span class="sxs-lookup"><span data-stu-id="0f447-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="0f447-125">Прекращает последующие операции отрисовки для компонента:</span><span class="sxs-lookup"><span data-stu-id="0f447-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="0f447-126">После первой отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0f447-126">After the first render.</span></span>
   * <span data-ttu-id="0f447-127">Если [`ShouldRender`](#suppress-ui-refreshing) имеет значение `false`.</span><span class="sxs-lookup"><span data-stu-id="0f447-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="0f447-128">Выполните сборку отличий дерева отрисовки и отрисуйте компонент.</span><span class="sxs-lookup"><span data-stu-id="0f447-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="0f447-129">Подождите, пока модель DOM обновится.</span><span class="sxs-lookup"><span data-stu-id="0f447-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="0f447-130">Вызовите [`OnAfterRender{Async}`](#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="0f447-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Жизненный цикл процесса отрисовки](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="0f447-132">Вызовы [`StateHasChanged`](#state-changes) разработчиком приводят к отрисовке.</span><span class="sxs-lookup"><span data-stu-id="0f447-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="0f447-133">Для получения дополнительной информации см. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="0f447-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="0f447-134">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="0f447-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="0f447-135">До указания параметров</span><span class="sxs-lookup"><span data-stu-id="0f447-135">Before parameters are set</span></span>

<span data-ttu-id="0f447-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки или из параметров маршрута.</span><span class="sxs-lookup"><span data-stu-id="0f447-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="0f447-137">Переопределяя метод, код разработчика может напрямую взаимодействовать с параметрами <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="0f447-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="0f447-138">В следующем примере <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> присваивает `value` значение параметра`Param`, если анализ параметра маршрута для `Param` выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="0f447-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="0f447-139">Если `value` не равно `null`, значение отображается компонентом.</span><span class="sxs-lookup"><span data-stu-id="0f447-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="0f447-140">Несмотря на то, что [сопоставление параметров маршрута не учитывает регистр](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> сопоставляется только с именами параметров, учитывающих регистр, в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="0f447-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="0f447-141">В следующем примере для получения значения требуется использовать `/{Param?}`, а не `/{param?}`.</span><span class="sxs-lookup"><span data-stu-id="0f447-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="0f447-142">Если в этом сценарии используется `/{param?}`, функция <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> возвращает значение `false`, а `message` не задается в качестве строкового параметра.</span><span class="sxs-lookup"><span data-stu-id="0f447-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="0f447-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="0f447-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="0f447-144"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит набор значений параметров для компонента при каждом вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f447-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="0f447-145">Реализация <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> по умолчанию задает значение каждого свойства с [атрибутом `[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) или [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute), имеющим соответствующее значение в <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="0f447-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="0f447-146">Параметры, у которых нет соответствующего значения в <xref:Microsoft.AspNetCore.Components.ParameterView>, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="0f447-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="0f447-147">Если [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="0f447-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="0f447-148">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="0f447-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="0f447-149">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="0f447-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0f447-150">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="0f447-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="0f447-151">Методы инициализации компонента</span><span class="sxs-lookup"><span data-stu-id="0f447-151">Component initialization methods</span></span>

<span data-ttu-id="0f447-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f447-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="0f447-153">Используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.</span><span class="sxs-lookup"><span data-stu-id="0f447-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="0f447-154">Для синхронной операции переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="0f447-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="0f447-155">Чтобы выполнить асинхронную операцию, переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и используйте в операции оператор [`await`](/dotnet/csharp/language-reference/operators/await).</span><span class="sxs-lookup"><span data-stu-id="0f447-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="0f447-156">Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/fundamentals/signalr#render-mode), вызывают <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *дважды*:</span><span class="sxs-lookup"><span data-stu-id="0f447-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="0f447-157">Один раз, когда компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="0f447-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="0f447-158">Второй раз, когда браузер устанавливает соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="0f447-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="0f447-159">Чтобы предотвратить выполнение кода разработчика в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="0f447-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="0f447-160">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено.</span><span class="sxs-lookup"><span data-stu-id="0f447-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="0f447-161">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="0f447-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="0f447-162">Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="0f447-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="0f447-163">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="0f447-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0f447-164">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="0f447-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="0f447-165">После указания параметров</span><span class="sxs-lookup"><span data-stu-id="0f447-165">After parameters are set</span></span>

<span data-ttu-id="0f447-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:</span><span class="sxs-lookup"><span data-stu-id="0f447-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="0f447-167">После инициализации компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f447-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="0f447-168">Когда родительский компонент повторно отрисовывается и предоставляет:</span><span class="sxs-lookup"><span data-stu-id="0f447-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="0f447-169">Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.</span><span class="sxs-lookup"><span data-stu-id="0f447-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="0f447-170">Любые параметры сложных типов.</span><span class="sxs-lookup"><span data-stu-id="0f447-170">Any complex-typed parameters.</span></span> <span data-ttu-id="0f447-171">Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.</span><span class="sxs-lookup"><span data-stu-id="0f447-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="0f447-172">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f447-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="0f447-173">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="0f447-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0f447-174">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="0f447-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="0f447-175">После отрисовки компонента</span><span class="sxs-lookup"><span data-stu-id="0f447-175">After component render</span></span>

<span data-ttu-id="0f447-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="0f447-177">В этот момент указываются ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="0f447-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="0f447-178">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="0f447-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="0f447-179">Параметр `firstRender` для <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="0f447-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="0f447-180">Устанавливается в значение `true` при первой отрисовке экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="0f447-181">Может использоваться, чтобы гарантировать однократное выполнение инициализации.</span><span class="sxs-lookup"><span data-stu-id="0f447-181">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="0f447-182">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="0f447-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="0f447-183">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="0f447-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="0f447-184">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0f447-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="0f447-185">Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="0f447-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="0f447-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *не вызываются при предварительной отрисовке на сервере*.</span><span class="sxs-lookup"><span data-stu-id="0f447-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="0f447-187">Методы вызываются, когда компонент отрисовывается в интерактивном режиме после завершения предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0f447-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="0f447-188">При предварительной отрисовке приложения происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="0f447-188">When the app prerenders:</span></span>

1. <span data-ttu-id="0f447-189">Компонент выполняется на сервере для создания статической HTML-разметки в HTTP-ответе.</span><span class="sxs-lookup"><span data-stu-id="0f447-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="0f447-190">На этом этапе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> не вызываются.</span><span class="sxs-lookup"><span data-stu-id="0f447-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="0f447-191">При запуске `blazor.server.js` или `blazor.webassembly.js` в браузере компонент перезапускается в режиме интерактивной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0f447-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="0f447-192">После перезапуска компонента **вызываются** методы <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, так как приложение больше не находится на этапе предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="0f447-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="0f447-193">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="0f447-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="0f447-194">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="0f447-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="0f447-195">Подавление обновления пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="0f447-195">Suppress UI refreshing</span></span>

<span data-ttu-id="0f447-196">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="0f447-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="0f447-197">Если реализация возвращает `true`, пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="0f447-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="0f447-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> вызывается каждый раз при отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="0f447-199">Даже при переопределении <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонент всегда проходит первоначальную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="0f447-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="0f447-200">Для получения дополнительной информации см. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="0f447-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="0f447-201">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="0f447-201">State changes</span></span>

<span data-ttu-id="0f447-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="0f447-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="0f447-203">В соответствующих случаях вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> приводит к повторной отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="0f447-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для методов <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="0f447-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="0f447-205">Для получения дополнительной информации см. <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="0f447-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="0f447-206">Для получения дополнительной информации см. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="0f447-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="0f447-207">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="0f447-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="0f447-208">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="0f447-209">Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью.</span><span class="sxs-lookup"><span data-stu-id="0f447-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="0f447-210">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="0f447-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="0f447-211">Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="0f447-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="0f447-212">В компоненте `FetchData` шаблонов Blazor <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> переопределяется для асинхронного получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="0f447-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="0f447-213">Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="0f447-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="0f447-214">Когда элемент `Task`, возвращенный <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, завершается, компонент отрисовывается повторно с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="0f447-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="0f447-215">`Pages/FetchData.razor` в шаблоне Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="0f447-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="0f447-216">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="0f447-216">Handle errors</span></span>

<span data-ttu-id="0f447-217">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="0f447-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="0f447-218">Повторное подключение с отслеживанием состояния после предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="0f447-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="0f447-219">В приложении Blazor Server, если <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> имеет значение <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="0f447-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="0f447-220">После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным.</span><span class="sxs-lookup"><span data-stu-id="0f447-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="0f447-221">Если метод жизненного цикла [`OnInitialized{Async}`](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:</span><span class="sxs-lookup"><span data-stu-id="0f447-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="0f447-222">Когда компонент предварительно отрисовывается статически.</span><span class="sxs-lookup"><span data-stu-id="0f447-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="0f447-223">После установления соединения с сервером.</span><span class="sxs-lookup"><span data-stu-id="0f447-223">After the server connection has been established.</span></span>

<span data-ttu-id="0f447-224">Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.</span><span class="sxs-lookup"><span data-stu-id="0f447-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="0f447-225">Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="0f447-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="0f447-226">Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.</span><span class="sxs-lookup"><span data-stu-id="0f447-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="0f447-227">Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="0f447-228">Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.</span><span class="sxs-lookup"><span data-stu-id="0f447-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="0f447-229">В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:</span><span class="sxs-lookup"><span data-stu-id="0f447-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="0f447-230">Дополнительные сведения об элементе <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> см. в разделе <xref:blazor/fundamentals/signalr#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="0f447-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="0f447-231">Обнаружение предварительной отрисовки приложения</span><span class="sxs-lookup"><span data-stu-id="0f447-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="0f447-232">Удаление компонента с использованием `IDisposable`</span><span class="sxs-lookup"><span data-stu-id="0f447-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="0f447-233">Если компонент реализует <xref:System.IDisposable>, платформа вызывает [метод удаления](/dotnet/standard/garbage-collection/implementing-dispose) при удалении компонента из пользовательского интерфейса, и вы можете освободить неуправляемые ресурсы.</span><span class="sxs-lookup"><span data-stu-id="0f447-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="0f447-234">Удаление можно выполнить в любое время, в том числе при [инициализации компонента](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="0f447-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="0f447-235">Следующий компонент реализует <xref:System.IDisposable> с использованием директивы Razor [`@implements`](xref:mvc/views/razor#implements):</span><span class="sxs-lookup"><span data-stu-id="0f447-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

```razor
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

<span data-ttu-id="0f447-236">Если объект требуется удалить, при вызове <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> можно использовать лямбда-выражение для удаления объекта:</span><span class="sxs-lookup"><span data-stu-id="0f447-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="0f447-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="0f447-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="0f447-238">Предыдущий пример находится в <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span><span class="sxs-lookup"><span data-stu-id="0f447-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="0f447-239">Для задач асинхронного удаления используйте `DisposeAsync` вместо <xref:System.IDisposable.Dispose>:</span><span class="sxs-lookup"><span data-stu-id="0f447-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="0f447-240">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="0f447-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="0f447-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="0f447-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="0f447-242">Отмена подписки на обработчики событий из событий .NET.</span><span class="sxs-lookup"><span data-stu-id="0f447-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="0f447-243">В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="0f447-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="0f447-244">Подход с использованием частного поля и лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="0f447-244">Private field and lambda approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* <span data-ttu-id="0f447-245">Подход с использованием частного метода</span><span class="sxs-lookup"><span data-stu-id="0f447-245">Private method approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

<span data-ttu-id="0f447-246">При использовании [анонимных функций](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), методов или выражений нет необходимости реализовывать <xref:System.IDisposable> и отсоединять делегаты.</span><span class="sxs-lookup"><span data-stu-id="0f447-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="0f447-247">Однако отсоединение делегата может стать проблемой, **когда объект, предоставляющий событие, переживает время существования компонента, регистрирующего делегат**.</span><span class="sxs-lookup"><span data-stu-id="0f447-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="0f447-248">В этом случае возникает утечка памяти, поскольку зарегистрированный делегат сохраняет исходный объект в активном состоянии.</span><span class="sxs-lookup"><span data-stu-id="0f447-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="0f447-249">Поэтому следует использовать только следующие подходы, если известно, что делегат события быстро удаляется.</span><span class="sxs-lookup"><span data-stu-id="0f447-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="0f447-250">Если неизвестно точное время существования объектов, требующих удаления, подключите метод делегата и правильно удалите делегат, как показано в предыдущих примерах.</span><span class="sxs-lookup"><span data-stu-id="0f447-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="0f447-251">Подход с использованием анонимного лямбда-метода (явное удаление не требуется)</span><span class="sxs-lookup"><span data-stu-id="0f447-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="0f447-252">Подход с использованием анонимного лямбда-выражения (явное удаление не требуется)</span><span class="sxs-lookup"><span data-stu-id="0f447-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="0f447-253">Полный пример предыдущего кода с анонимными лямбда-выражениями представлен в <xref:blazor/forms-validation#validator-components>.</span><span class="sxs-lookup"><span data-stu-id="0f447-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="0f447-254">Дополнительные сведения см. в статье [Очистка неуправляемых ресурсов](/dotnet/standard/garbage-collection/unmanaged) и следующих разделах, в которых описана реализация методов `Dispose` и `DisposeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0f447-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="0f447-255">Отменяемая фоновая операция</span><span class="sxs-lookup"><span data-stu-id="0f447-255">Cancelable background work</span></span>

<span data-ttu-id="0f447-256">Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных.</span><span class="sxs-lookup"><span data-stu-id="0f447-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="0f447-257">В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций.</span><span class="sxs-lookup"><span data-stu-id="0f447-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="0f447-258">Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.</span><span class="sxs-lookup"><span data-stu-id="0f447-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="0f447-259">Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="0f447-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="0f447-260">Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.</span><span class="sxs-lookup"><span data-stu-id="0f447-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="0f447-261">Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.</span><span class="sxs-lookup"><span data-stu-id="0f447-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="0f447-262">Необходимо изменить приоритет выполняемых в данный момент задач.</span><span class="sxs-lookup"><span data-stu-id="0f447-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="0f447-263">Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.</span><span class="sxs-lookup"><span data-stu-id="0f447-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="0f447-264">Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре графика выполнения фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="0f447-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="0f447-265">Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="0f447-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="0f447-266">Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="0f447-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="0f447-267">При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.</span><span class="sxs-lookup"><span data-stu-id="0f447-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="0f447-268">После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.</span><span class="sxs-lookup"><span data-stu-id="0f447-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="0f447-269">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="0f447-269">In the following example:</span></span>

* <span data-ttu-id="0f447-270">`await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.</span><span class="sxs-lookup"><span data-stu-id="0f447-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="0f447-271">`BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.</span><span class="sxs-lookup"><span data-stu-id="0f447-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="0f447-272">События повторного подключения Blazor Server</span><span class="sxs-lookup"><span data-stu-id="0f447-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="0f447-273">События жизненного цикла компонента, описываемые в этой статье, работают отдельно от [обработчиков событий повторного подключения Blazor Server](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="0f447-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="0f447-274">Когда приложение Blazor Server теряет подключение SignalR к клиенту, прерываются только операции обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="0f447-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="0f447-275">Они возобновляются при восстановлении подключения.</span><span class="sxs-lookup"><span data-stu-id="0f447-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="0f447-276">Дополнительные сведения о конфигурации и событиях обработчика канала см. в статье <xref:blazor/fundamentals/signalr>.</span><span class="sxs-lookup"><span data-stu-id="0f447-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
