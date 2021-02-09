---
title: Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly
author: pranavkm
description: Советы по повышению производительности приложений ASP.NET Core Blazor WebAssembly и избежанию распространенных проблем с производительностью.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 58a87bc5413523fdf052a9e1c41196bb8b0ab457
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529973"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="15b7d-103">Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="15b7d-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="15b7d-104">Авторы: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy) и [Стив Сандерсон](https://github.com/SteveSandersonMS) (Steve Sanderson)</span><span class="sxs-lookup"><span data-stu-id="15b7d-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="15b7d-105">Среда Blazor WebAssembly тщательно спроектирована и оптимизирована для обеспечения высокой производительности в наиболее часто применимых сценариях пользовательского интерфейса в приложениях.</span><span class="sxs-lookup"><span data-stu-id="15b7d-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="15b7d-106">Но качество результатов зависит еще и от того, насколько правильно разработчики используют шаблоны и функции.</span><span class="sxs-lookup"><span data-stu-id="15b7d-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="15b7d-107">Давайте рассмотрим следующие аспекты.</span><span class="sxs-lookup"><span data-stu-id="15b7d-107">Consider the following aspects:</span></span>

* <span data-ttu-id="15b7d-108">**Пропускная способность среды выполнения.** Код .NET выполняется в интерпретаторе в среде выполнения WebAssembly, что ограничивает пропускную способность ЦП.</span><span class="sxs-lookup"><span data-stu-id="15b7d-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="15b7d-109">Для ресурсоемких сценариев стоит применить в приложении [оптимизацию скорости отрисовки](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="15b7d-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="15b7d-110">**Время запуска.** Приложение передает среду выполнения .NET в браузер, поэтому важно использовать функции, которые позволяют [уменьшить размер скачиваемого приложения](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="15b7d-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="15b7d-111">Оптимизация скорости отрисовки</span><span class="sxs-lookup"><span data-stu-id="15b7d-111">Optimize rendering speed</span></span>

<span data-ttu-id="15b7d-112">В следующих разделах собраны рекомендации по уменьшению рабочей нагрузки для отрисовки и повышению скорости реагирования пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="15b7d-113">Применение этих советов часто позволяет ускорить отрисовку пользовательского интерфейса *в десять и более раз*.</span><span class="sxs-lookup"><span data-stu-id="15b7d-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="15b7d-114">Избегайте ненужной отрисовки поддеревьев компонентов</span><span class="sxs-lookup"><span data-stu-id="15b7d-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="15b7d-115">В среде выполнения все компоненты занимают определенное место в иерархии.</span><span class="sxs-lookup"><span data-stu-id="15b7d-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="15b7d-116">Корневой компонент содержит дочерние компоненты.</span><span class="sxs-lookup"><span data-stu-id="15b7d-116">A root component has child components.</span></span> <span data-ttu-id="15b7d-117">Каждый из этих дочерних элементов может содержать собственные дочерние компоненты, и так далее.</span><span class="sxs-lookup"><span data-stu-id="15b7d-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="15b7d-118">При возникновении события, например при нажатии кнопки, Blazor выбирает компоненты для визуализации по следующему алгоритму:</span><span class="sxs-lookup"><span data-stu-id="15b7d-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

1. <span data-ttu-id="15b7d-119">Возникшее событие отправляется в тот компонент, который создал обработчик такого события.</span><span class="sxs-lookup"><span data-stu-id="15b7d-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="15b7d-120">После выполнения обработчика событий соответствующий компонент повторно отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="15b7d-120">After executing the event handler, that component is rerendered.</span></span>
1. <span data-ttu-id="15b7d-121">При каждой повторной отрисовке компонент предоставляет новую копию значений параметров каждому из своих дочерних компонентов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
1. <span data-ttu-id="15b7d-122">Получив новый набор значений параметров, каждый компонент самостоятельно решает, нужно ли выполнять повторную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="15b7d-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="15b7d-123">По умолчанию это происходит в том случае, если значения параметров могли измениться (например, если они являются изменяемыми объектами).</span><span class="sxs-lookup"><span data-stu-id="15b7d-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="15b7d-124">И далее два последних шага рекурсивно повторяются для всех компонентов вниз по иерархии.</span><span class="sxs-lookup"><span data-stu-id="15b7d-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="15b7d-125">Во многих случаях это приводит к перерисовке всего дерева.</span><span class="sxs-lookup"><span data-stu-id="15b7d-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="15b7d-126">Это означает, что события для компонентов высокого уровня могут приводить к высоким затратам на перерисовку всех компонентов вниз по иерархии, начиная с целевого.</span><span class="sxs-lookup"><span data-stu-id="15b7d-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="15b7d-127">Прервать такой процесс и избежать рекурсивной перерисовки всех компонентов в поддереве можно одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="15b7d-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

* <span data-ttu-id="15b7d-128">Присвойте всем параметрам каждого компонента примитивные неизменяемые типы (`string`, `int`, `bool`, `DateTime` и т. п.).</span><span class="sxs-lookup"><span data-stu-id="15b7d-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="15b7d-129">В этом случае встроенная логика отслеживания изменений будет пропускать перерисовку, когда значения параметров не изменились явным образом.</span><span class="sxs-lookup"><span data-stu-id="15b7d-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="15b7d-130">Если для отрисовки дочернего компонента используется `<Customer CustomerId="@item.CustomerId" />`, где значение `CustomerId` имеет тип `int`, то он будет перерисовываться только при изменении `item.CustomerId`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
* <span data-ttu-id="15b7d-131">Если вы не можете обойтись только примитивными значениями параметров (например, вам нужны пользовательские типы моделей, обратные вызовы событий или значения <xref:Microsoft.AspNetCore.Components.RenderFragment>), можно переопределить <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> для принятия собственного решения об отрисовке при изменениях, как описано в разделе об [использовании `ShouldRender`](#use-of-shouldrender).</span><span class="sxs-lookup"><span data-stu-id="15b7d-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="15b7d-132">Пропуская перерисовку целых поддеревьев, вы сможете устранить почти все затраты на отрисовку при возникновении событий.</span><span class="sxs-lookup"><span data-stu-id="15b7d-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="15b7d-133">Возможно, вы захотите исключить конкретные дочерние компоненты, чтобы пропустить перерисовку соответствующей ветви пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="15b7d-134">Это допустимый способ снижения затрат на отрисовку родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="15b7d-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="15b7d-135">Использование `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="15b7d-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="15b7d-136">При создании компонента, который используется только в пользовательском интерфейсе и не изменяется после первоначальной отрисовки (независимо от значений параметров), настройте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> так, чтобы он возвращал значение `false`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="15b7d-137">Если перерисовка компонента требуется только при некоторых изменениях значений его параметров, вы можете использовать скрытые поля для отслеживания важной информации с целью обнаружения изменений.</span><span class="sxs-lookup"><span data-stu-id="15b7d-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="15b7d-138">В следующем примере значение `shouldRender` вычисляется по наличию таких изменений или преобразований, которые требуют перерисовки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="15b7d-139">`prevOutboundFlightId` и `prevInboundFlightId` отслеживают сведения, которые определяют необходимость следующего обновления.</span><span class="sxs-lookup"><span data-stu-id="15b7d-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="15b7d-140">В приведенном выше коде обработчик событий может установить для `shouldRender` значение `true`, чтобы выполнить перерисовку компонента после обработки события.</span><span class="sxs-lookup"><span data-stu-id="15b7d-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="15b7d-141">Для большинства компонентов такой уровень управления вручную не требуется.</span><span class="sxs-lookup"><span data-stu-id="15b7d-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="15b7d-142">Достаточно лишь обеспечить пропуск отрисовки для тех поддеревьев, отрисовка которых особенно трудоемка и может приводить к задержкам в пользовательском интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="15b7d-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="15b7d-143">Для получения дополнительной информации см. <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="15b7d-144">Виртуализация</span><span class="sxs-lookup"><span data-stu-id="15b7d-144">Virtualization</span></span>

<span data-ttu-id="15b7d-145">Если в некотором цикле выполняется отрисовка значительной части пользовательского интерфейса, например списка или сетки с тысячами записей, само по себе количество операций может приводить к задержке в отрисовке пользовательского интерфейса и ухудшению взаимодействия с пользователем.</span><span class="sxs-lookup"><span data-stu-id="15b7d-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="15b7d-146">Учитывая, что пользователь одновременно без прокрутки видит лишь небольшое количество таких элементов, зачастую излишне тратить время на отрисовку тех элементов, которые пока не видны.</span><span class="sxs-lookup"><span data-stu-id="15b7d-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="15b7d-147">Чтобы решить эту проблему, в Blazor предоставляется встроенный компонент `Virtualize`, который эмулирует поведение отображения и прокрутки для произвольно большого списка, фактически отображая только те из них, которые попадают в текущее окно просмотра с учетом прокрутки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-147">To address this, Blazor provides the `Virtualize` component that creates the appearance and scroll behaviors of an arbitrarily-large list but only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="15b7d-148">Например, вы сможете создать в приложении список со 100 000 записей, тратя ресурсы отрисовки только на те 20 элементов, которые видимы в конкретный момент времени.</span><span class="sxs-lookup"><span data-stu-id="15b7d-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="15b7d-149">Использование компонента `Virtualize` позволяет повысить производительность пользовательского интерфейса в несколько раз.</span><span class="sxs-lookup"><span data-stu-id="15b7d-149">Use of the `Virtualize` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="15b7d-150">Для получения дополнительной информации см. <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-150">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="15b7d-151">Создание простых и оптимизированных компонентов</span><span class="sxs-lookup"><span data-stu-id="15b7d-151">Create lightweight, optimized components</span></span>

<span data-ttu-id="15b7d-152">Для большинства компонентов Blazor не требуются агрессивные действия по оптимизации.</span><span class="sxs-lookup"><span data-stu-id="15b7d-152">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="15b7d-153">Это связано с тем, что большинство компонентов присутствуют в пользовательском интерфейсе в малом количестве копий и не отрисовываются с высокой частотой.</span><span class="sxs-lookup"><span data-stu-id="15b7d-153">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="15b7d-154">Например, компоненты `@page` и компоненты, представляющие высокоуровневые элементы пользовательского интерфейса (диалоговые окна, формы и т. п.), обычно отображаются только в одно экземпляре и отрисовываются только в ответ на жест пользователя.</span><span class="sxs-lookup"><span data-stu-id="15b7d-154">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="15b7d-155">Такие компоненты не создают высокой нагрузки на подсистему отрисовки, что позволяет свободно использовать любые сочетания функций платформы, не беспокоясь о производительности при отрисовке.</span><span class="sxs-lookup"><span data-stu-id="15b7d-155">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="15b7d-156">Но есть и другие, не менее распространенные сценарии, в которых компоненты создаются в большом количестве экземпляров.</span><span class="sxs-lookup"><span data-stu-id="15b7d-156">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="15b7d-157">Пример:</span><span class="sxs-lookup"><span data-stu-id="15b7d-157">For example:</span></span>

* <span data-ttu-id="15b7d-158">Большие вложенные формы могут иметь сотни полей для ввода, меток и других элементов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-158">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
* <span data-ttu-id="15b7d-159">Сетки могут содержать тысячи ячеек.</span><span class="sxs-lookup"><span data-stu-id="15b7d-159">Grids may have thousands of cells.</span></span>
* <span data-ttu-id="15b7d-160">Точечные диаграммы могут содержать миллионы точек данных.</span><span class="sxs-lookup"><span data-stu-id="15b7d-160">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="15b7d-161">Если каждая такая единица будет существовать в виде отдельного экземпляра компонента, их количество приведет к критической нагрузке на производительность отрисовки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-161">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="15b7d-162">В этом разделе собраны рекомендации по упрощению систем с такими компонентами, которые позволят сохранить скорость работы и реагирования пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-162">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="15b7d-163">Не создавайте тысячи экземпляров компонентов</span><span class="sxs-lookup"><span data-stu-id="15b7d-163">Avoid thousands of component instances</span></span>

<span data-ttu-id="15b7d-164">Каждый компонент является обособленным объектом, который можно отрисовывать отдельно, независимо от его родительских и дочерних элементов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-164">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="15b7d-165">Правильный выбор способа для структурирования пользовательского интерфейса в иерархии компонентов позволяет контролировать степень детализации для отрисовки пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-165">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="15b7d-166">Это может по-разному влиять на производительность.</span><span class="sxs-lookup"><span data-stu-id="15b7d-166">This can be either good or bad for performance.</span></span>

* <span data-ttu-id="15b7d-167">Разделяя пользовательский интерфейс на большое число компонентов, вы уменьшаете объем перерисовки при возникновении событий пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-167">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="15b7d-168">Например, когда пользователь нажимает кнопку в строке таблицы, можно вместо всей страницы или таблицы повторно отрисовать только одну строку.</span><span class="sxs-lookup"><span data-stu-id="15b7d-168">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
* <span data-ttu-id="15b7d-169">Но каждый дополнительный компонент требует дополнительный объем памяти и времени ЦП для поддержки индивидуального состояния и жизненного цикла визуализации.</span><span class="sxs-lookup"><span data-stu-id="15b7d-169">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="15b7d-170">При настройке производительности Blazor WebAssembly в .NET 5 мы пришли к выводу, что на отрисовку каждого экземпляра компонента требуется около 0,06 мс.</span><span class="sxs-lookup"><span data-stu-id="15b7d-170">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="15b7d-171">Расчеты проводились для простого компонента, который принимает три параметра, при выполнении на типичном ноутбуке.</span><span class="sxs-lookup"><span data-stu-id="15b7d-171">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="15b7d-172">На внутреннем уровне издержки связаны в первую очередь с извлечением из словарей состояния для каждого компонента, а также с передачей и получением параметров.</span><span class="sxs-lookup"><span data-stu-id="15b7d-172">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="15b7d-173">Умножая это значение на число компонентов, легко заметить, что 2000 экземпляров компонентов увеличат время отрисовки на 0,12 секунд, что для пользователя означает уже заметное замедление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-173">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="15b7d-174">Вы можете сделать компоненты более простыми, чтобы поддерживать большее их число, но часто лучшим подходом будет уменьшение количества компонентов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-174">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="15b7d-175">В следующих разделах мы опишем оба подхода.</span><span class="sxs-lookup"><span data-stu-id="15b7d-175">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="15b7d-176">Дочерние компоненты, встроенные в родительские</span><span class="sxs-lookup"><span data-stu-id="15b7d-176">Inline child components into their parents</span></span>

<span data-ttu-id="15b7d-177">Давайте рассмотрим такой компонент, который отрисовывает серию дочерних компонентов:</span><span class="sxs-lookup"><span data-stu-id="15b7d-177">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="15b7d-178">В приведенном выше примере кода компонент `<ChatMessageDisplay>` определяется в файле `ChatMessageDisplay.razor` следующего содержания:</span><span class="sxs-lookup"><span data-stu-id="15b7d-178">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="15b7d-179">Представленный выше пример хорошо работает, пока не придется отобразить одновременно несколько тысяч сообщений.</span><span class="sxs-lookup"><span data-stu-id="15b7d-179">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="15b7d-180">Чтобы отобразить все эти сообщения, мы можем *отказаться* от создания отдельных компонентов `ChatMessageDisplay`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-180">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="15b7d-181">Вместо этого мы встроим этот компонент отрисовки напрямую в родительский компонент:</span><span class="sxs-lookup"><span data-stu-id="15b7d-181">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="15b7d-182">Так мы избежим накладных расходов на отрисовку большого числа дочерних компонентов, но зато потеряем возможность отрисовывать их независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="15b7d-182">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="15b7d-183">Определение повторно используемого делегата `RenderFragments` в коде</span><span class="sxs-lookup"><span data-stu-id="15b7d-183">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="15b7d-184">Возможно, вы создаете дочерние компоненты просто для того, чтобы повторно использовать логику отрисовки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-184">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="15b7d-185">В этом сценарии есть возможность повторно использовать логику отрисовки без объявления фактических компонентов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-185">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="15b7d-186">В блоке `@code` любого компонента объявите <xref:Microsoft.AspNetCore.Components.RenderFragment>, который возвращает пользовательский интерфейс и может вызываться из любого места:</span><span class="sxs-lookup"><span data-stu-id="15b7d-186">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    private RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="15b7d-187">Как показано в предыдущем примере, компоненты могут создавать разметку как в коде блока `@code`, так и вне его.</span><span class="sxs-lookup"><span data-stu-id="15b7d-187">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="15b7d-188">Такой подход использует объявление делегата <xref:Microsoft.AspNetCore.Components.RenderFragment>, который можно отрисовывать в выходном потоке отрисовки компонента, в том числе из нескольких разных мест.</span><span class="sxs-lookup"><span data-stu-id="15b7d-188">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="15b7d-189">Важно, чтобы этот делегат принимал параметр с именем `__builder` и типом <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, на основе которого компилятор Razor будет создавать инструкции отрисовки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-189">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="15b7d-190">Если вы хотите использовать его в нескольких компонентах, можно объявить элемент `public static`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-190">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="15b7d-191">Теперь его можно вызывать из любого компонента, даже не являющегося родственным.</span><span class="sxs-lookup"><span data-stu-id="15b7d-191">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="15b7d-192">Такая методика удобна для создания библиотек повторно используемых фрагментов кода разметки, которые позволяют избавиться от накладных расходов на создание каждого компонента.</span><span class="sxs-lookup"><span data-stu-id="15b7d-192">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="15b7d-193">Делегаты <xref:Microsoft.AspNetCore.Components.RenderFragment> могут принимать параметры.</span><span class="sxs-lookup"><span data-stu-id="15b7d-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="15b7d-194">Так вы можете создать структуру, эквивалентную компоненту `ChatMessageDisplay` из предыдущего примера:</span><span class="sxs-lookup"><span data-stu-id="15b7d-194">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @ChatMessageDisplay(message)
    }
</div>

@code {
    private RenderFragment<ChatMessage> ChatMessageDisplay = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="15b7d-195">Такой подход позволяет повторно использовать логику отрисовки без дополнительных затрат на компоненты.</span><span class="sxs-lookup"><span data-stu-id="15b7d-195">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="15b7d-196">Но зато здесь невозможно независимо обновлять поддерево пользовательского интерфейса или пропускать визуализацию этого поддерева при отрисовке родительского элемента, так как мы убрали границу между компонентами.</span><span class="sxs-lookup"><span data-stu-id="15b7d-196">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

<span data-ttu-id="15b7d-197">Для нестатического поля, метода или свойства, на которые не может ссылаться инициализатор поля, например `TitleTemplate` в следующем примере, используйте свойство вместо поля для <xref:Microsoft.AspNetCore.Components.RenderFragment>:</span><span class="sxs-lookup"><span data-stu-id="15b7d-197">For a non-static field, method, or property that can't be referenced by a field initializer, such as `TitleTemplate` in the following example, use a property instead of a field for the <xref:Microsoft.AspNetCore.Components.RenderFragment>:</span></span>

```csharp
protected RenderFragment DisplayTitle => __builder =>
{
    <div>
        @TitleTemplate
    </div>   
};
```

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="15b7d-198">Не принимайте слишком много параметров</span><span class="sxs-lookup"><span data-stu-id="15b7d-198">Don't receive too many parameters</span></span>

<span data-ttu-id="15b7d-199">Если компонент повторяется очень часто, например сотни или тысячи раз, важно помнить о накладных расходах на передачу и получение каждого параметра.</span><span class="sxs-lookup"><span data-stu-id="15b7d-199">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="15b7d-200">Маловероятно, что большое число параметров само по себе ухудшит производительность, но оно может усложнить ситуацию при наличии других проблем.</span><span class="sxs-lookup"><span data-stu-id="15b7d-200">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="15b7d-201">Для компонента `<TableCell>`, который отображается 1000 раз в сетке, каждый дополнительный передаваемый параметр увеличивает время отрисовки примерно на 15 мс.</span><span class="sxs-lookup"><span data-stu-id="15b7d-201">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="15b7d-202">Если каждая ячейка принимает 10 параметров, то передача параметров займет около 150 мс на каждый компонент, то есть 150 000 мс (150 секунд) в целом, что сделает пользовательский интерфейс очень медленным.</span><span class="sxs-lookup"><span data-stu-id="15b7d-202">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="15b7d-203">Чтобы уменьшить эту нагрузку, объедините несколько параметров в пакет с помощью пользовательских классов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-203">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="15b7d-204">Например, компонент `<TableCell>` может принимать параметры так:</span><span class="sxs-lookup"><span data-stu-id="15b7d-204">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="15b7d-205">В предыдущем примере значение `Data` будет разным для каждой ячейки, но `Options` является общим для всех.</span><span class="sxs-lookup"><span data-stu-id="15b7d-205">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="15b7d-206">Разумеется, еще лучше будет отказаться от компонента `<TableCell>`, встроив его логику в родительский компонент.</span><span class="sxs-lookup"><span data-stu-id="15b7d-206">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="15b7d-207">Сделайте все каскадные параметры фиксированными</span><span class="sxs-lookup"><span data-stu-id="15b7d-207">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="15b7d-208">Компонент `<CascadingValue>` принимает необязательный параметр с именем `IsFixed`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-208">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

* <span data-ttu-id="15b7d-209">Если значение `IsFixed` равно `false` (вариант по умолчанию), то каждый получатель каскадного значения настраивает подписку для получения уведомлений об изменениях.</span><span class="sxs-lookup"><span data-stu-id="15b7d-209">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="15b7d-210">В этом примере каждый `[CascadingParameter]` обойдется **значительно дороже** обычного `[Parameter]` из-за затрат на отслеживания подписки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-210">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
* <span data-ttu-id="15b7d-211">Если значение `IsFixed` равно `true` (например, `<CascadingValue Value="@someValue" IsFixed="true">`), то получатели получают начальное значение, но *не настраивают* подписку для получения обновлений.</span><span class="sxs-lookup"><span data-stu-id="15b7d-211">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="15b7d-212">Тогда каждый элемент `[CascadingParameter]` будет достаточно легким и **не дороже**, чем обычный `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-212">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="15b7d-213">Старайтесь везде, где возможно, использовать `IsFixed="true"` в каскадных значениях.</span><span class="sxs-lookup"><span data-stu-id="15b7d-213">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="15b7d-214">Это не вызовет затруднений, если передаваемое значение не меняется со временем.</span><span class="sxs-lookup"><span data-stu-id="15b7d-214">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="15b7d-215">В типичной ситуации, когда компонент передает `this` в виде каскадного значения, следует использовать `IsFixed="true"`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-215">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="15b7d-216">Это даст огромную разницу в производительности, если большое число других компонентов получают это каскадное значение.</span><span class="sxs-lookup"><span data-stu-id="15b7d-216">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="15b7d-217">Для получения дополнительной информации см. <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-217">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="15b7d-218">Не используйте сплаттинг атрибутов с `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="15b7d-218">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="15b7d-219">Компоненты могут получать "несопоставленные" значения параметров с помощью флага <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>:</span><span class="sxs-lookup"><span data-stu-id="15b7d-219">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="15b7d-220">Такой подход позволяет передать элементу произвольные дополнительные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="15b7d-220">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="15b7d-221">Но этот подход довольно дорогой, поскольку отрисовщик будет выполнять следующие действия:</span><span class="sxs-lookup"><span data-stu-id="15b7d-221">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="15b7d-222">сопоставление всех полученных параметров с набором известных параметров для построения словаря;</span><span class="sxs-lookup"><span data-stu-id="15b7d-222">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="15b7d-223">отслеживание копий одного атрибута, которые переопределяют друг друга.</span><span class="sxs-lookup"><span data-stu-id="15b7d-223">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="15b7d-224">Вы можете использовать <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> для тех компонентов, которые не создают критической нагрузки на производительность, например редко повторяются.</span><span class="sxs-lookup"><span data-stu-id="15b7d-224">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="15b7d-225">Но для компонентов с большим масштабом отрисовки, таких как элементы большого списка или ячейки сетки, избегайте сплаттинга атрибутов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-225">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="15b7d-226">Для получения дополнительной информации см. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-226">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="15b7d-227">Реализуйте `SetParametersAsync` вручную</span><span class="sxs-lookup"><span data-stu-id="15b7d-227">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="15b7d-228">Одним из основных аспектов, влияющих на затраты отрисовки каждого компонента, является запись значений входящих параметров в свойства `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-228">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="15b7d-229">Для них отрисовщик использует отражение.</span><span class="sxs-lookup"><span data-stu-id="15b7d-229">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="15b7d-230">Этот механизм имеет некоторую оптимизацию, но отсутствие поддержки JIT в среде выполнения WebAssembly налагает определенные ограничения.</span><span class="sxs-lookup"><span data-stu-id="15b7d-230">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="15b7d-231">В некоторых крайних случаях следует избегать отражений и вручную реализовать собственную логику настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="15b7d-231">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="15b7d-232">Это может уместно в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="15b7d-232">This may be applicable when:</span></span>

* <span data-ttu-id="15b7d-233">у вас есть компонент, который отображается чрезвычайно часто (например, существуют сотни или тысячи его копий в пользовательском интерфейсе);</span><span class="sxs-lookup"><span data-stu-id="15b7d-233">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
* <span data-ttu-id="15b7d-234">компонент принимает много параметров;</span><span class="sxs-lookup"><span data-stu-id="15b7d-234">It accepts many parameters.</span></span>
* <span data-ttu-id="15b7d-235">вы заметили, что издержки на получение параметров заметно влияют на скорость реагирования пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="15b7d-235">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="15b7d-236">В таких случаях вы можете переопределить виртуальный метод <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> компонента и реализовать собственную логику для конкретного компонента.</span><span class="sxs-lookup"><span data-stu-id="15b7d-236">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="15b7d-237">Следующий пример намеренно создан так, чтобы избежать поиска в словаре:</span><span class="sxs-lookup"><span data-stu-id="15b7d-237">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="15b7d-238">В приведенном выше коде возвращение базового класса <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> запускает обычные методы жизненного цикла без повторного назначения параметров.</span><span class="sxs-lookup"><span data-stu-id="15b7d-238">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="15b7d-239">Как мы видим в приведенном выше коде, переопределение <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> и предоставление пользовательской логики достаточно сложны и трудоемки, поэтому мы не рекомендуем использовать этот подход в общем случае.</span><span class="sxs-lookup"><span data-stu-id="15b7d-239">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="15b7d-240">В исключительных ситуациях он может повысить производительность отрисовки на 20–25 %, но его стоит применять только в перечисленных выше сценариях.</span><span class="sxs-lookup"><span data-stu-id="15b7d-240">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="15b7d-241">Не активируйте события слишком часто</span><span class="sxs-lookup"><span data-stu-id="15b7d-241">Don't trigger events too rapidly</span></span>

<span data-ttu-id="15b7d-242">Некоторые события браузера происходят очень часто, например `onmousemove` и `onscroll` могут запускаться десятки или даже сотни раз в секунду.</span><span class="sxs-lookup"><span data-stu-id="15b7d-242">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="15b7d-243">В большинстве случаев вам не нужно так часто обновлять интерфейс.</span><span class="sxs-lookup"><span data-stu-id="15b7d-243">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="15b7d-244">Такое частое обновление может снижать скорость реагирования пользовательского интерфейса или создавать чрезмерную нагрузку на ЦП.</span><span class="sxs-lookup"><span data-stu-id="15b7d-244">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="15b7d-245">Вместо системных событий `@onmousemove` или `@onscroll` вам лучше применить механизм взаимодействия JS, чтобы зарегистрировать более редко вызываемый обратный вызов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-245">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="15b7d-246">Например, следующий компонент `MyComponent.razor` отображает текущее положение мыши только один раз в каждые 500 мс:</span><span class="sxs-lookup"><span data-stu-id="15b7d-246">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="15b7d-247">Соответствующий код JavaScript, который можно поместить на страницу `index.html` или загрузить в виде модуля ES6, регистрирует прослушиватель событий DOM.</span><span class="sxs-lookup"><span data-stu-id="15b7d-247">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="15b7d-248">В нашем примере прослушиватель событий использует [функцию Lodash `throttle`](https://lodash.com/docs/4.17.15#throttle), чтобы ограничить частоту вызовов:</span><span class="sxs-lookup"><span data-stu-id="15b7d-248">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="15b7d-249">Этот подход даст еще более заметный результат для Blazor Server, так как каждый вызов события требует доставки сообщения по сети.</span><span class="sxs-lookup"><span data-stu-id="15b7d-249">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="15b7d-250">Для Blazor WebAssembly это полезно тем, что может значительно снизить объем работы по отрисовке.</span><span class="sxs-lookup"><span data-stu-id="15b7d-250">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="15b7d-251">Оптимизируйте скорость взаимодействия в JavaScript</span><span class="sxs-lookup"><span data-stu-id="15b7d-251">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="15b7d-252">Передача вызовов между .NET и JavaScript требует дополнительных затрат по нескольким причинам:</span><span class="sxs-lookup"><span data-stu-id="15b7d-252">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

* <span data-ttu-id="15b7d-253">все вызовы по умолчанию асинхронны;</span><span class="sxs-lookup"><span data-stu-id="15b7d-253">By default, calls are asynchronous.</span></span>
* <span data-ttu-id="15b7d-254">все параметры и возвращаемые значения по умолчанию сериализуются в формат JSON.</span><span class="sxs-lookup"><span data-stu-id="15b7d-254">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="15b7d-255">Это нужно для того, чтобы создать простой для понимания механизм преобразования типов между .NET и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="15b7d-255">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="15b7d-256">Кроме того, в Blazor Server эти вызовы передаются по сети.</span><span class="sxs-lookup"><span data-stu-id="15b7d-256">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="15b7d-257">Не используйте чрезмерно подробные вызовы</span><span class="sxs-lookup"><span data-stu-id="15b7d-257">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="15b7d-258">Поскольку каждый вызов влечет за собой накладные расходы, будет полезно снизить их количество.</span><span class="sxs-lookup"><span data-stu-id="15b7d-258">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="15b7d-259">Давайте рассмотрим следующий код, который сохраняет коллекцию элементов в хранилище `localStorage` браузера:</span><span class="sxs-lookup"><span data-stu-id="15b7d-259">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="15b7d-260">В предшествующем примере для каждого элемента создается отдельный вызов для взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="15b7d-260">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="15b7d-261">Вместо этого можно ограничить взаимодействие с JS пределами одного вызова:</span><span class="sxs-lookup"><span data-stu-id="15b7d-261">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="15b7d-262">Ниже определяется соответствующая функция JavaScript:</span><span class="sxs-lookup"><span data-stu-id="15b7d-262">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="15b7d-263">Для Blazor WebAssembly это важно лишь при очень большом числе вызовов для взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="15b7d-263">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="15b7d-264">Старайтесь использовать синхронные вызовы</span><span class="sxs-lookup"><span data-stu-id="15b7d-264">Consider making synchronous calls</span></span>

<span data-ttu-id="15b7d-265">Вызовы для взаимодействия с JavaScript по умолчанию асинхронны, независимо от поддержки синхронности в вызываемом коде.</span><span class="sxs-lookup"><span data-stu-id="15b7d-265">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="15b7d-266">Это нужно для совместимости всех компонентов как с Blazor WebAssembly, так и с Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="15b7d-266">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="15b7d-267">Дело в том, что в Blazor Server все вызовы для взаимодействия с JavaScript передаются через сетевое подключение, поэтому они должны быть асинхронными.</span><span class="sxs-lookup"><span data-stu-id="15b7d-267">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="15b7d-268">Если же вам точно известно, что приложение будет выполняться только на Blazor WebAssembly, вы можете переключиться на использование синхронных вызовов для взаимодействия с JavaScript.</span><span class="sxs-lookup"><span data-stu-id="15b7d-268">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="15b7d-269">Они создают чуть меньше нагрузки, чем асинхронные вызовы, и снижают число циклов отрисовки благодаря отсутствию промежуточного состояния на время ожидания результатов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-269">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="15b7d-270">Чтобы выполнить синхронный вызов из .NET к JavaScript, приведите <xref:Microsoft.JSInterop.IJSRuntime> к <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span><span class="sxs-lookup"><span data-stu-id="15b7d-270">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="15b7d-271">При работе с `IJSObjectReference` синхронный вызов выполняется приведением к `IJSInProcessObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-271">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="15b7d-272">Чтобы выполнить синхронный вызов из JavaScript к .NET, используйте `DotNet.invokeMethod` вместо `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-272">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="15b7d-273">Синхронные вызовы возможны, если соблюдаются следующие условия:</span><span class="sxs-lookup"><span data-stu-id="15b7d-273">Synchronous calls work if:</span></span>

* <span data-ttu-id="15b7d-274">приложение работает только на Blazor WebAssembly, но не на Blazor Server;</span><span class="sxs-lookup"><span data-stu-id="15b7d-274">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="15b7d-275">вызываемся функция возвращает значение синхронным способом (то есть не является методом `async` и не возвращает <xref:System.Threading.Tasks.Task> в .NET или `Promise` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="15b7d-275">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="15b7d-276">Для получения дополнительной информации см. <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-276">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="15b7d-277">Старайтесь использовать демаршалированные вызовы</span><span class="sxs-lookup"><span data-stu-id="15b7d-277">Consider making unmarshalled calls</span></span>

<span data-ttu-id="15b7d-278">При работе в Blazor WebAssembly есть возможность выполнять демаршалированные вызовы из .NET к JavaScript.</span><span class="sxs-lookup"><span data-stu-id="15b7d-278">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="15b7d-279">Так называются синхронные вызовы, которые не используют сериализацию в JSON для аргументов или возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="15b7d-279">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="15b7d-280">Все аспекты управления памятью и преобразования представлений для .NET и JavaScript остаются на усмотрение разработчика.</span><span class="sxs-lookup"><span data-stu-id="15b7d-280">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="15b7d-281">Хотя использование `IJSUnmarshalledRuntime` обеспечивает наименьшие издержки по сравнению с другими подходами к взаимодействию с JS, интерфейсы API JavaScript, необходимые для взаимодействия с этими интерфейсами API, в настоящее время не документированы и могут подвернуться критическим изменениям в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="15b7d-281">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="15b7d-282">Уменьшайте размер скачиваемого приложения</span><span class="sxs-lookup"><span data-stu-id="15b7d-282">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="15b7d-283">Обрезка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="15b7d-283">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="15b7d-284">[Обрезка неиспользуемых сборок в приложении Blazor WebAssembly](xref:blazor/host-and-deploy/configure-trimmer) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="15b7d-284">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="15b7d-285">По умолчанию средство обрезки выполняется при публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="15b7d-285">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="15b7d-286">Чтобы воспользоваться обрезкой, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-286">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="15b7d-287">Компоновка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="15b7d-287">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="15b7d-288">[Компоновка приложения Blazor WebAssembly](xref:blazor/host-and-deploy/configure-linker) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="15b7d-288">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="15b7d-289">По умолчанию компоновщик промежуточного языка (IL) включается только при сборке в конфигурации `Release`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-289">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="15b7d-290">Чтобы воспользоваться этой возможностью, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-290">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="15b7d-291">Использование System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="15b7d-291">Use System.Text.Json</span></span>

<span data-ttu-id="15b7d-292">Реализация взаимодействия с JS в Blazor основана на <xref:System.Text.Json>, высокопроизводительной библиотеке сериализации JSON, занимающей мало места в памяти.</span><span class="sxs-lookup"><span data-stu-id="15b7d-292">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="15b7d-293">Использование <xref:System.Text.Json> не приводит к увеличению размера полезных данных в приложении, в отличие от добавления одной или нескольких альтернативных библиотек JSON.</span><span class="sxs-lookup"><span data-stu-id="15b7d-293">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="15b7d-294">Руководство по миграции см. в статье [Миграция с `Newtonsoft.Json` на `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="15b7d-294">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="15b7d-295">Сборки с отложенной загрузкой</span><span class="sxs-lookup"><span data-stu-id="15b7d-295">Lazy load assemblies</span></span>

<span data-ttu-id="15b7d-296">Загрузка сборок во время выполнения на том этапе, когда они необходимы для маршрута.</span><span class="sxs-lookup"><span data-stu-id="15b7d-296">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="15b7d-297">Для получения дополнительной информации см. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-297">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="15b7d-298">Сжатие</span><span class="sxs-lookup"><span data-stu-id="15b7d-298">Compression</span></span>

<span data-ttu-id="15b7d-299">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="15b7d-299">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="15b7d-300">Blazor использует сервер для согласования содержимого и предоставления статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="15b7d-300">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="15b7d-301">После развертывания приложения убедитесь в том, что приложение предоставляет сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="15b7d-301">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="15b7d-302">В браузере откройте вкладку "Сеть" в Средствах для разработчиков и убедитесь в том, что файлы предоставляются с `Content-Encoding: br` или `Content-Encoding: gz`.</span><span class="sxs-lookup"><span data-stu-id="15b7d-302">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="15b7d-303">Если узел не предоставляет сжатые файлы, выполните инструкции в разделе <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="15b7d-303">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="15b7d-304">Отключение неиспользуемых функций</span><span class="sxs-lookup"><span data-stu-id="15b7d-304">Disable unused features</span></span>

<span data-ttu-id="15b7d-305">Среда выполнения Blazor WebAssembly включает в себя следующие функции .NET, которые можно отключить, если они не требуются приложению, для уменьшения размера полезных данных:</span><span class="sxs-lookup"><span data-stu-id="15b7d-305">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="15b7d-306">Для обеспечения правильности сведений о часовом поясе включается файл данных.</span><span class="sxs-lookup"><span data-stu-id="15b7d-306">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="15b7d-307">Если приложению не нужна эта функция, ее можно отключить, присвоив свойству MSBuild `BlazorEnableTimeZoneSupport` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-307">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="15b7d-308">По умолчанию Blazor WebAssembly содержит ресурсы глобализации, необходимые для отображения значений, таких как даты и денежные единицы, на языке пользователя и с его региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="15b7d-308">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="15b7d-309">Если приложению не требуется локализация, можно [настроить поддержку инвариантных языка и региональных параметров](xref:blazor/globalization-localization), основанных на языке и региональных параметрах `en-US`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-309">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="15b7d-310">Для правильной работы таких интерфейсов API, как <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>, включаются сведения о параметрах сортировки.</span><span class="sxs-lookup"><span data-stu-id="15b7d-310">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="15b7d-311">Если вы уверены, что приложению не нужны сведения о параметрах сортировки, эту функцию можно отключить, присвоив свойству MSBuild `BlazorWebAssemblyPreserveCollationData` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="15b7d-311">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
