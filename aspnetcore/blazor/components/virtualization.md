---
title: Виртуализация компонентов ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать виртуализацию компонентов в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: c2b16f4bc8e69265b1d59082c3f029541f277d8b
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252293"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="c9030-103">Виртуализация компонентов ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c9030-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="c9030-104">Автор: [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="c9030-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c9030-105">Повысьте воспринимаемую производительность отрисовки компонентов с помощью встроенной поддержки виртуализации платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="c9030-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="c9030-106">Виртуализация — это метод отображения только видимых в данный момент частей пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="c9030-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="c9030-107">Например, виртуализация удобна в случае, когда в приложении должен быть отрисован длинный список элементов и в любой конкретный момент времени должно быть видимым только подмножество элементов.</span><span class="sxs-lookup"><span data-stu-id="c9030-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="c9030-108">Blazor предоставляет [компонент `Virtualize`](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601), который можно использовать для добавления виртуализации в компоненты приложения.</span><span class="sxs-lookup"><span data-stu-id="c9030-108">Blazor provides the [`Virtualize` component](xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601) that can be used to add virtualization to an app's components.</span></span>

<span data-ttu-id="c9030-109">Компонент `Virtualize` можно использовать в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="c9030-109">The `Virtualize` component can be used when:</span></span>

* <span data-ttu-id="c9030-110">при отрисовке набора элементов данных в цикле;</span><span class="sxs-lookup"><span data-stu-id="c9030-110">Rendering a set of data items in a loop.</span></span>
* <span data-ttu-id="c9030-111">если большинство элементов не видны из-за настроек прокрутки;</span><span class="sxs-lookup"><span data-stu-id="c9030-111">Most of the items aren't visible due to scrolling.</span></span>
* <span data-ttu-id="c9030-112">если все отображаемые элементы имеют одинаковый размер.</span><span class="sxs-lookup"><span data-stu-id="c9030-112">The rendered items are exactly the same size.</span></span> <span data-ttu-id="c9030-113">Когда пользователь прокручивает список до произвольной точки, этот компонент вычисляет все видимые элементы, чтобы отобразить их.</span><span class="sxs-lookup"><span data-stu-id="c9030-113">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="c9030-114">Без виртуализации обычный список может использовать цикл C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) для отрисовки каждого элемента в списке:</span><span class="sxs-lookup"><span data-stu-id="c9030-114">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    }
</div>
```

<span data-ttu-id="c9030-115">Если список содержит тысячи элементов, его отрисовка может занять много времени.</span><span class="sxs-lookup"><span data-stu-id="c9030-115">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="c9030-116">При этом не исключена заметная задержка отображения пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="c9030-116">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="c9030-117">Вместо отрисовки каждого элемента списка по отдельности замените цикл [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) компонентом `Virtualize` и укажите источник фиксированного элемента с помощью <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c9030-117">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c9030-118">Отрисовываются только элементы, видимые в данный момент:</span><span class="sxs-lookup"><span data-stu-id="c9030-118">Only the items that are currently visible are rendered:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Details="@flight.Summary" />
    </Virtualize>
</div>
```

<span data-ttu-id="c9030-119">Вместо указания контекста для компонента с помощью `Context` можно использовать значение `context` в шаблоне содержимого элемента.</span><span class="sxs-lookup"><span data-stu-id="c9030-119">If not specifying a context to the component with `Context`, use the `context` value in the item content template:</span></span>

```razor
<div style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights">
        <FlightSummary @key="context.FlightId" Details="@context.Summary" />
    </Virtualize>
</div>
```

> [!NOTE]
> <span data-ttu-id="c9030-120">Процесс сопоставления объектов модели с элементами и компонентами можно контролировать с помощью атрибута [`@key`](xref:mvc/views/razor#key) директивы.</span><span class="sxs-lookup"><span data-stu-id="c9030-120">The mapping process of model objects to elements and components can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="c9030-121">`@key` гарантирует, что алгоритм сравнения сохранит элементы или компоненты на основе значения ключа.</span><span class="sxs-lookup"><span data-stu-id="c9030-121">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span>
>
> <span data-ttu-id="c9030-122">Дополнительные сведения см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="c9030-122">For more information, see the following articles:</span></span>
>
> * <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>
> * [<span data-ttu-id="c9030-123">Справочник по синтаксису Razor для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9030-123">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor#key)

<span data-ttu-id="c9030-124">Компонент `Virtualize`:</span><span class="sxs-lookup"><span data-stu-id="c9030-124">The `Virtualize` component:</span></span>

* <span data-ttu-id="c9030-125">вычисляет количество подлежащих отрисовке элементов на основе высоты контейнера и размера отображаемых элементов;</span><span class="sxs-lookup"><span data-stu-id="c9030-125">Calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>
* <span data-ttu-id="c9030-126">пересчитывает и повторно отрисовывает элементы при прокрутке пользователем;</span><span class="sxs-lookup"><span data-stu-id="c9030-126">Recalculates and rerenders items as the user scrolls.</span></span>
* <span data-ttu-id="c9030-127">извлекает из внешнего API только тот срез записей, который соответствует текущей видимой области, не скачивая все данные из коллекции.</span><span class="sxs-lookup"><span data-stu-id="c9030-127">Only fetches the slice of records from an external API that correspond to the current visible region, instead of downloading all of the data from the collection.</span></span>

<span data-ttu-id="c9030-128">Содержимое элемента для компонента `Virtualize` может включать в себя следующее:</span><span class="sxs-lookup"><span data-stu-id="c9030-128">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="c9030-129">обычный код HTML и код Razor, как показано в предыдущем примере;</span><span class="sxs-lookup"><span data-stu-id="c9030-129">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="c9030-130">один или несколько компонентов Razor;</span><span class="sxs-lookup"><span data-stu-id="c9030-130">One or more Razor components.</span></span>
* <span data-ttu-id="c9030-131">сочетание компонентов HTML/Razor и Razor.</span><span class="sxs-lookup"><span data-stu-id="c9030-131">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="c9030-132">Делегат поставщика элементов</span><span class="sxs-lookup"><span data-stu-id="c9030-132">Item provider delegate</span></span>

<span data-ttu-id="c9030-133">Если вы не хотите загружать все элементы в память, можно указать метод делегата поставщика элементов для параметра <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> компонента, который асинхронно извлекает запрошенные элементы по запросу.</span><span class="sxs-lookup"><span data-stu-id="c9030-133">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A?displayProperty=nameWithType> parameter that asynchronously retrieves the requested items on demand.</span></span> <span data-ttu-id="c9030-134">В следующем примере метод `LoadEmployees` предоставляет элементы компоненту `Virtualize`.</span><span class="sxs-lookup"><span data-stu-id="c9030-134">In the following example, the `LoadEmployees` method provides the items to the `Virtualize` component:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="c9030-135">Поставщик элементов получает <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, который указывает необходимое количество элементов, начиная с заданного начального индекса.</span><span class="sxs-lookup"><span data-stu-id="c9030-135">The items provider receives an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderRequest>, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="c9030-136">Затем поставщик элементов извлекает запрошенные элементы из базы данных или другой службы и возвращает их в виде <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> вместе с количеством всех элементов.</span><span class="sxs-lookup"><span data-stu-id="c9030-136">The items provider then retrieves the requested items from a database or other service and returns them as an <xref:Microsoft.AspNetCore.Components.Web.Virtualization.ItemsProviderResult%601> along with a count of the total items.</span></span> <span data-ttu-id="c9030-137">Поставщик элементов может извлекать элементы с каждым запросом или кэшировать их, чтобы они были доступны.</span><span class="sxs-lookup"><span data-stu-id="c9030-137">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span>

<span data-ttu-id="c9030-138">Компонент `Virtualize` может принимать только **один источник элемента** из параметров, поэтому не пытайтесь одновременно использовать поставщик элементов и назначить коллекцию для `Items`.</span><span class="sxs-lookup"><span data-stu-id="c9030-138">A `Virtualize` component can only accept **one item source** from its parameters, so don't attempt to simultaneously use an items provider and assign a collection to `Items`.</span></span> <span data-ttu-id="c9030-139">Если назначаются оба значения, создается <xref:System.InvalidOperationException>, когда параметры компонента задаются во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c9030-139">If both are assigned, an <xref:System.InvalidOperationException> is thrown when the component's parameters are set at runtime.</span></span>

<span data-ttu-id="c9030-140">В следующем примере метода `LoadEmployees` демонстрируется загрузка сотрудников из `EmployeeService` (не показано).</span><span class="sxs-lookup"><span data-stu-id="c9030-140">The following `LoadEmployees` method example loads employees from an `EmployeeService` (not shown):</span></span>

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

<span data-ttu-id="c9030-141"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> указывает компоненту на необходимость повторного запроса данных из <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span><span class="sxs-lookup"><span data-stu-id="c9030-141"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.RefreshDataAsync%2A?displayProperty=nameWithType> instructs the component to rerequest data from its <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemsProvider%2A>.</span></span> <span data-ttu-id="c9030-142">Это полезно в тех случаях, когда внешние данные изменяются.</span><span class="sxs-lookup"><span data-stu-id="c9030-142">This is useful when external data changes.</span></span> <span data-ttu-id="c9030-143">Вызывать этот метод при использовании <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A> не требуется.</span><span class="sxs-lookup"><span data-stu-id="c9030-143">There's no need to call this when using <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Items%2A>.</span></span>

## <a name="placeholder"></a><span data-ttu-id="c9030-144">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="c9030-144">Placeholder</span></span>

<span data-ttu-id="c9030-145">Поскольку запрос элементов из удаленного источника данных может занимать некоторое время, можно отрисовать заполнитель с содержимым элемента.</span><span class="sxs-lookup"><span data-stu-id="c9030-145">Because requesting items from a remote data source might take some time, you have the option to render a placeholder with item content:</span></span>

* <span data-ttu-id="c9030-146">Используйте <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) для отображения содержимого до тех пор, пока не будут доступны данные элемента.</span><span class="sxs-lookup"><span data-stu-id="c9030-146">Use a <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.Placeholder%2A> (`<Placeholder>...</Placeholder>`) to display content until the item data is available.</span></span>
* <span data-ttu-id="c9030-147">Чтобы задать шаблон элемента для списка, используйте <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c9030-147">Use <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemContent%2A?displayProperty=nameWithType> to set the item template for the list.</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a><span data-ttu-id="c9030-148">Размер элемента</span><span class="sxs-lookup"><span data-stu-id="c9030-148">Item size</span></span>

<span data-ttu-id="c9030-149">Размер каждого элемента в пикселях можно задать с помощью <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (по умолчанию: 50):</span><span class="sxs-lookup"><span data-stu-id="c9030-149">The size of each item in pixels can be set with <xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.ItemSize%2A?displayProperty=nameWithType> (default: 50):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="c9030-150">Количество элементов в нерабочей области</span><span class="sxs-lookup"><span data-stu-id="c9030-150">Overscan count</span></span>

<span data-ttu-id="c9030-151"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> определяет количество дополнительных элементов, отрисовываемых до и после видимой области.</span><span class="sxs-lookup"><span data-stu-id="c9030-151"><xref:Microsoft.AspNetCore.Components.Web.Virtualization.Virtualize%601.OverscanCount%2A?displayProperty=nameWithType> determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="c9030-152">Этот параметр позволяет уменьшить частоту отрисовки во время прокрутки.</span><span class="sxs-lookup"><span data-stu-id="c9030-152">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="c9030-153">Однако более высокие значения приводят к отображению большего числа элементов на странице (по умолчанию: 3):</span><span class="sxs-lookup"><span data-stu-id="c9030-153">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

## <a name="state-changes"></a><span data-ttu-id="c9030-154">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="c9030-154">State changes</span></span>

<span data-ttu-id="c9030-155">При внесении изменений в элементы, отрисовываемые компонентом `Virtualize`, вызовите метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для принудительной оценки и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="c9030-155">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span> <span data-ttu-id="c9030-156">Для получения дополнительной информации см. <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="c9030-156">For more information, see <xref:blazor/components/rendering>.</span></span>
