---
title: Отрисовка компонента Blazor ASP.NET Core
author: guardrex
description: Сведения об отрисовке компонента Razor в приложениях ASP.NET Core Blazor, в том числе о моменте, когда необходимо вызывать метод StateHasChanged.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1a4d4116b8a6d9266bbacbbdd8f20dc49b4e1db0
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253861"
---
# <a name="aspnet-core-no-locblazor-component-rendering"></a><span data-ttu-id="b7d51-103">Отрисовка компонента Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7d51-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="b7d51-104">Автор: [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)</span><span class="sxs-lookup"><span data-stu-id="b7d51-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="b7d51-105">Компоненты *должны* отрисовываться при первом добавлении в иерархию компонентов их родительским компонентом.</span><span class="sxs-lookup"><span data-stu-id="b7d51-105">Components *must* render when they're first added to the component hierarchy by their parent component.</span></span> <span data-ttu-id="b7d51-106">Это единственный случай, когда отрисовка компонента строго обязательна.</span><span class="sxs-lookup"><span data-stu-id="b7d51-106">This is the only time that a component strictly must render.</span></span>

<span data-ttu-id="b7d51-107">Компоненты, *могут* отрисовываться в любое другое время в соответствии с собственной логикой и соглашениями.</span><span class="sxs-lookup"><span data-stu-id="b7d51-107">Components *may* choose to render at any other time according to their own logic and conventions.</span></span>

## <a name="conventions-for-componentbase"></a><span data-ttu-id="b7d51-108">Соглашения для `ComponentBase`</span><span class="sxs-lookup"><span data-stu-id="b7d51-108">Conventions for `ComponentBase`</span></span>

<span data-ttu-id="b7d51-109">По умолчанию компоненты Razor (`.razor`) наследуются от базового класса <xref:Microsoft.AspNetCore.Components.ComponentBase>, который содержит логику, вызывающую повторную отрисовку в следующие моменты времени:</span><span class="sxs-lookup"><span data-stu-id="b7d51-109">By default, Razor components (`.razor`) inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="b7d51-110">После применения обновленного набора параметров из родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="b7d51-110">After applying an updated set of parameters from a parent component.</span></span>
* <span data-ttu-id="b7d51-111">После применения обновленного значения для каскадного параметра.</span><span class="sxs-lookup"><span data-stu-id="b7d51-111">After applying an updated value for a cascading parameter.</span></span>
* <span data-ttu-id="b7d51-112">После уведомления о событии и вызова одного из его собственных обработчиков событий.</span><span class="sxs-lookup"><span data-stu-id="b7d51-112">After notification of an event and invoking one of its own event handlers.</span></span>
* <span data-ttu-id="b7d51-113">После вызова его собственного метода <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="b7d51-113">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method.</span></span>

<span data-ttu-id="b7d51-114">Компоненты, унаследованные от <xref:Microsoft.AspNetCore.Components.ComponentBase> пропускают повторную отрисовку при обновлении параметров в том случае, если выполняется одно из следующих условий:</span><span class="sxs-lookup"><span data-stu-id="b7d51-114">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="b7d51-115">Все значения параметров имеют известные неизменяемые примитивные типы (например, `int`, `string`, `DateTime`) и не изменялись с момента установки предыдущего набора параметров.</span><span class="sxs-lookup"><span data-stu-id="b7d51-115">All of the parameter values are of known immutable primitive types (for example, `int`, `string`, `DateTime`) and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="b7d51-116">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонента возвращает `false`.</span><span class="sxs-lookup"><span data-stu-id="b7d51-116">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="b7d51-117">Дополнительные сведения о методе <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> см. в разделе <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span><span class="sxs-lookup"><span data-stu-id="b7d51-117">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="b7d51-118">Управление потоком отрисовки</span><span class="sxs-lookup"><span data-stu-id="b7d51-118">Control the rendering flow</span></span>

<span data-ttu-id="b7d51-119">В большинстве случаев соглашения для <xref:Microsoft.AspNetCore.Components.ComponentBase> определяют корректное подмножество повторных отрисовок компонента после наступления события.</span><span class="sxs-lookup"><span data-stu-id="b7d51-119">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="b7d51-120">Разработчикам как правило не требуется предоставлять вручную логику, указывающую платформе, какие компоненты и когда следует повторно отрисовывать.</span><span class="sxs-lookup"><span data-stu-id="b7d51-120">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="b7d51-121">В целом, соглашения для платформы определяют, что получающий событие компонент повторно отрисовывает себя. В этом случае инициируется рекурсивная повторная отрисовка компонентов-потомков, значения параметров которых могли измениться.</span><span class="sxs-lookup"><span data-stu-id="b7d51-121">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="b7d51-122">Дополнительные сведения о том, как соглашения для платформы влияют на производительность и как оптимизировать иерархию компонентов приложения, см. в разделе <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span><span class="sxs-lookup"><span data-stu-id="b7d51-122">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="b7d51-123">Когда следует вызывать метод `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="b7d51-123">When to call `StateHasChanged`</span></span>

<span data-ttu-id="b7d51-124">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> позволяет в любое время запускать отрисовку.</span><span class="sxs-lookup"><span data-stu-id="b7d51-124">The <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A?displayProperty=nameWithType> method allows you to trigger a render at any time.</span></span> <span data-ttu-id="b7d51-125">Тем не менее, метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> не следует вызывать без необходимости. Это распространенная ошибка, которая влечет за собой ненужные затраты ресурсов на отрисовку.</span><span class="sxs-lookup"><span data-stu-id="b7d51-125">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake, because it imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="b7d51-126">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> *не требуется* вызывать в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="b7d51-126">You should *not* need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="b7d51-127">Стандартная синхронная или асинхронная обработка событий, поскольку <xref:Microsoft.AspNetCore.Components.ComponentBase> запускает отрисовку для большинства стандартных обработчиков событий.</span><span class="sxs-lookup"><span data-stu-id="b7d51-127">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="b7d51-128">Реализация типовой синхронной или асинхронной логики жизненного цикла, например [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) или [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), поскольку <xref:Microsoft.AspNetCore.Components.ComponentBase> запускает отрисовку для типовых событий жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="b7d51-128">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="b7d51-129">Тем не менее, вызов метода <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может требоваться в случаях, которые описываются в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="b7d51-129">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described by the following sections:</span></span>

* [<span data-ttu-id="b7d51-130">Использование нескольких асинхронных стадий в асинхронном обработчике</span><span class="sxs-lookup"><span data-stu-id="b7d51-130">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="b7d51-131">Получение вызова извне к системе обработки событий Blazor</span><span class="sxs-lookup"><span data-stu-id="b7d51-131">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="b7d51-132">Отрисовка компонента за пределами поддерева, которое повторно отрисовывается в связи с определенным событием</span><span class="sxs-lookup"><span data-stu-id="b7d51-132">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="b7d51-133">Использование нескольких асинхронных стадий в асинхронном обработчике</span><span class="sxs-lookup"><span data-stu-id="b7d51-133">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="b7d51-134">Рассмотрим следующий компонент `Counter`, который обновляет счетчик четыре раза для каждого щелчка.</span><span class="sxs-lookup"><span data-stu-id="b7d51-134">Consider the following `Counter` component, which updates the count four times on each click.</span></span>

<span data-ttu-id="b7d51-135">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7d51-135">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private async Task IncrementCount()
    {
        currentCount++;
        // Renders here automatically

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        StateHasChanged();

        await Task.Delay(1000);
        currentCount++;
        // Renders here automatically
    }
}
```

<span data-ttu-id="b7d51-136">Из-за особенностей определения задач в .NET получатель <xref:System.Threading.Tasks.Task> может наблюдать только за его окончательным завершением, а не за промежуточными асинхронными состояниями.</span><span class="sxs-lookup"><span data-stu-id="b7d51-136">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="b7d51-137">Таким образом, <xref:Microsoft.AspNetCore.Components.ComponentBase> может активировать повторную отрисовку только при первом возвращении <xref:System.Threading.Tasks.Task> и при завершении <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="b7d51-137">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="b7d51-138">Информацию о необходимости повторной отрисовки в промежуточных точках он не получает.</span><span class="sxs-lookup"><span data-stu-id="b7d51-138">It can't know to rerender at other intermediate points.</span></span> <span data-ttu-id="b7d51-139">Соответственно, если требуется повторная отрисовка в промежуточных точках, следует использовать <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="b7d51-139">If you want to rerender at intermediate points, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

### <a name="receiving-a-call-from-something-external-to-the-no-locblazor-rendering-and-event-handling-system"></a><span data-ttu-id="b7d51-140">Получение вызова извне к системе обработки событий Blazor</span><span class="sxs-lookup"><span data-stu-id="b7d51-140">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="b7d51-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> имеет сведения только о собственных методах жизненного цикла и событиях, вызываемых Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7d51-141"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="b7d51-142">Соответственно <xref:Microsoft.AspNetCore.Components.ComponentBase> не располагает информацией о других событиях, которые могут возникать в вашем коде.</span><span class="sxs-lookup"><span data-stu-id="b7d51-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in your code.</span></span> <span data-ttu-id="b7d51-143">Например, Blazor не имеет сведений о любых событиях C#, вызываемых пользовательским хранилищем данных.</span><span class="sxs-lookup"><span data-stu-id="b7d51-143">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="b7d51-144">Чтобы такие события вызывали повторную отрисовку для отображения обновленных значений в пользовательском интерфейсе, используйте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span><span class="sxs-lookup"><span data-stu-id="b7d51-144">In order for such events to trigger rerendering to display updated values in the UI, use <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="b7d51-145">Рассмотрим также следующий компонент `Counter`, который использует <xref:System.Timers.Timer?displayProperty=fullName> для обновления счетчика с установленным интервалом и вызывает метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b7d51-145">In another use case, consider the following `Counter` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update the count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI.</span></span>

<span data-ttu-id="b7d51-146">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b7d51-146">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"
@using System.Timers
@implements IDisposable

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="b7d51-147">В предыдущем примере `OnTimerCallback` должен вызвать метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>, поскольку Blazor не знает об изменениях `currentCount` в обратном вызове.</span><span class="sxs-lookup"><span data-stu-id="b7d51-147">In the preceding example, `OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span> <span data-ttu-id="b7d51-148">`OnTimerCallback` выполняется за пределами управляемого потока отрисовки или уведомления о событии Blazor.</span><span class="sxs-lookup"><span data-stu-id="b7d51-148">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span>

<span data-ttu-id="b7d51-149">Аналогичным образом, поскольку обратный вызов выполняется вне контекста синхронизации Blazor, необходимо упаковать логику в <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType>, чтобы переместить ее в контекст синхронизации модуля отрисовки.</span><span class="sxs-lookup"><span data-stu-id="b7d51-149">Similarly, because the callback is invoked outside Blazor's synchronization context, it's necessary to wrap the logic in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="b7d51-150">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться только из контекста синхронизации модуля отрисовки и в противном случае приводит к возникновению исключения.</span><span class="sxs-lookup"><span data-stu-id="b7d51-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="b7d51-151">Это поведение эквивалентно маршалингу потока пользовательского интерфейса на других платформах пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b7d51-151">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="b7d51-152">Отрисовка компонента за пределами поддерева, которое повторно отрисовывается в связи с определенным событием</span><span class="sxs-lookup"><span data-stu-id="b7d51-152">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="b7d51-153">В пользовательском интерфейсе возможны отправка события в один компонент, изменение некоторого состояния и возникновение необходимости в повторной отрисовке совершенно другого компонента, который не является потомком компонента, получающего событие.</span><span class="sxs-lookup"><span data-stu-id="b7d51-153">Your UI might involve dispatching an event to one component, changing some state, and needing to rerender a completely different component that isn't a descendant of the one receiving the event.</span></span>

<span data-ttu-id="b7d51-154">Одним из способов работы в таких сценариях является *управление состоянием*, например с использованием службы DI, внедренной в несколько компонентов.</span><span class="sxs-lookup"><span data-stu-id="b7d51-154">One way to deal with this scenario is to have a *state management* class, perhaps as a DI service, injected into multiple components.</span></span> <span data-ttu-id="b7d51-155">Когда один компонент вызывает метод в диспетчере состояний, диспетчер состояний может вызвать событие C#, которое затем получается независимым компонентом.</span><span class="sxs-lookup"><span data-stu-id="b7d51-155">When one component calls a method on the state manager, the state manager can raise a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="b7d51-156">Поскольку эти события C# выходят за пределы конвейера отрисовки Blazor, следует вызвать метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для других компонентов, которые необходимо обработать в ответ на события диспетчера состояний.</span><span class="sxs-lookup"><span data-stu-id="b7d51-156">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="b7d51-157">Это аналогично приведенному ранее варианту с <xref:System.Timers.Timer?displayProperty=fullName> в [предыдущем разделе](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system).</span><span class="sxs-lookup"><span data-stu-id="b7d51-157">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system) section.</span></span> <span data-ttu-id="b7d51-158">Поскольку стек вызовов выполнения как правило остается в контексте синхронизации модуля отрисовки, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> обычно не требуется.</span><span class="sxs-lookup"><span data-stu-id="b7d51-158">Since the execution call stack typically remains on the renderer's synchronization context, <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="b7d51-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> требуется только в том случае, если логика выходит из контекста синхронизации, например в результате вызова <xref:System.Threading.Tasks.Task.ContinueWith%2A> для <xref:System.Threading.Tasks.Task> или ожидания <xref:System.Threading.Tasks.Task> с [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span><span class="sxs-lookup"><span data-stu-id="b7d51-159"><xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
