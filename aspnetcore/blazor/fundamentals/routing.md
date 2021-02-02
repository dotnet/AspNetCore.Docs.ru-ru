---
title: Маршрутизация ASP.NET Core Blazor
author: guardrex
description: Узнайте, как управлять маршрутизацией запросов в приложениях и как использовать компонент NavLink в приложениях Blazor для навигации.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 74886eae7431045e56de535b5221040bd56cdc2d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751572"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="72f06-103">Маршрутизация ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="72f06-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="72f06-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="72f06-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="72f06-105">В этой статье описывается, как управлять маршрутизацией запросов и как использовать компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> для создания навигационных ссылок в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="72f06-105">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="72f06-106">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="72f06-106">Route templates</span></span>

<span data-ttu-id="72f06-107">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет выполнять маршрутизацию в компоненты Razor в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="72f06-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="72f06-108">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> используется в компоненте `App` приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="72f06-108">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="72f06-109">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-109">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

<span data-ttu-id="72f06-110">При компиляции компонента Razor (`.razor`) с [директивой `@page`](xref:mvc/views/razor#page) созданный класс компонента предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="72f06-110">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="72f06-111">При запуске приложения выполняется проверка сборки, указанной как `AppAssembly` маршрутизатора, для сбора сведений о маршрутах для компонентов приложения с <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="72f06-111">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="72f06-112">В среде выполнения компонент <xref:Microsoft.AspNetCore.Components.RouteView> выполняет следующие операции:</span><span class="sxs-lookup"><span data-stu-id="72f06-112">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="72f06-113">получает <xref:Microsoft.AspNetCore.Components.RouteData> от <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе со всеми параметрами маршрута;</span><span class="sxs-lookup"><span data-stu-id="72f06-113">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="72f06-114">преобразовывает указанный компонент для просмотра с его [макетом](xref:blazor/layouts), включая все вложенные макеты.</span><span class="sxs-lookup"><span data-stu-id="72f06-114">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="72f06-115">При необходимости укажите параметр <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> с классом макета для компонентов, которые не задают макет, с помощью [директивы `@layout`](xref:blazor/layouts#specify-a-layout-in-a-component).</span><span class="sxs-lookup"><span data-stu-id="72f06-115">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="72f06-116">В шаблонах проекта Blazor платформы в качестве макета приложения по умолчанию указывается компонент `MainLayout` (`Shared/MainLayout.razor`).</span><span class="sxs-lookup"><span data-stu-id="72f06-116">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="72f06-117">Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="72f06-117">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="72f06-118">Компоненты поддерживают несколько шаблонов маршрутов с помощью нескольких [директив `@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="72f06-118">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="72f06-119">Приведенный ниже пример компонента загружается при запросах к `/BlazorRoute` и `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="72f06-119">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="72f06-120">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-120">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="72f06-121">Для правильного разрешения URL-адресов приложение должно содержать тег `<base>` в файле `wwwroot/index.html` (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href`.</span><span class="sxs-lookup"><span data-stu-id="72f06-121">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="72f06-122">Дополнительные сведения см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="72f06-122">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="72f06-123">Предоставление пользовательского содержимого, когда содержимое не найдено</span><span class="sxs-lookup"><span data-stu-id="72f06-123">Provide custom content when content isn't found</span></span>

<span data-ttu-id="72f06-124">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.</span><span class="sxs-lookup"><span data-stu-id="72f06-124">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="72f06-125">В компоненте `App` задайте пользовательское содержимое в шаблоне <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="72f06-125">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="72f06-126">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-126">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="72f06-127">Теги `<NotFound>` могут содержать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="72f06-127">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="72f06-128">Сведения о применении макета по умолчанию к содержимому <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> см. в разделе <xref:blazor/layouts#default-layout>.</span><span class="sxs-lookup"><span data-stu-id="72f06-128">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="72f06-129">Маршрутизация к компонентам из нескольких сборок</span><span class="sxs-lookup"><span data-stu-id="72f06-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="72f06-130">Используйте параметр <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, чтобы указать дополнительные сборки для компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>, которые следует учитывать при поиске маршрутизируемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="72f06-130">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="72f06-131">В дополнение к сборке, указанной в `AppAssembly`, проверяются дополнительные сборки.</span><span class="sxs-lookup"><span data-stu-id="72f06-131">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="72f06-132">В приведенном ниже примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой [библиотеке классов компонентов](xref:blazor/components/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="72f06-132">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="72f06-133">В приведенном ниже примере <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> приводится поддержка маршрутизации для `Component1`.</span><span class="sxs-lookup"><span data-stu-id="72f06-133">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="72f06-134">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-134">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="72f06-135">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="72f06-135">Route parameters</span></span>

<span data-ttu-id="72f06-136">Маршрутизатор использует параметры маршрута для заполнения соответствующих [параметров компонента](xref:blazor/components/index#component-parameters) с тем же именем.</span><span class="sxs-lookup"><span data-stu-id="72f06-136">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="72f06-137">В именах параметров маршрута регистр не учитывается.</span><span class="sxs-lookup"><span data-stu-id="72f06-137">Route parameter names are case insensitive.</span></span> <span data-ttu-id="72f06-138">В приведенном ниже примере параметр `text` присваивает значение сегмента маршрута свойству `Text` компонента.</span><span class="sxs-lookup"><span data-stu-id="72f06-138">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="72f06-139">Если запрос выполняется для `/RouteParameter/amazing`, содержимое тега `<h1>` отображается как `Blazor is amazing!`.</span><span class="sxs-lookup"><span data-stu-id="72f06-139">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="72f06-140">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-140">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="72f06-141">Поддерживаются необязательные параметры.</span><span class="sxs-lookup"><span data-stu-id="72f06-141">Optional parameters are supported.</span></span> <span data-ttu-id="72f06-142">В следующем примере необязательный параметр `text` назначает значение сегмента маршрута свойству `Text` компонента.</span><span class="sxs-lookup"><span data-stu-id="72f06-142">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="72f06-143">Если сегмента нет, для `Text` устанавливается значение `fantastic`.</span><span class="sxs-lookup"><span data-stu-id="72f06-143">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="72f06-144">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-144">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="72f06-145">Необязательные параметры не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="72f06-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="72f06-146">В приведенном ниже примере применяются две [директивы `@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="72f06-146">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="72f06-147">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="72f06-147">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="72f06-148">Вторая директива присваивает значение параметра маршрута `{text}` свойству `Text` компонента.</span><span class="sxs-lookup"><span data-stu-id="72f06-148">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="72f06-149">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-149">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="72f06-150">Чтобы разрешить переход к тому же компоненту с другим значением необязательного параметра, используйте [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) вместо [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="72f06-150">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="72f06-151">Принимая во внимание предыдущий пример, используйте `OnParametersSet`, когда пользователь должен иметь возможность переходить от `/RouteParameter` к `/RouteParameter/amazing` или от `/RouteParameter/amazing` к `/RouteParameter`:</span><span class="sxs-lookup"><span data-stu-id="72f06-151">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="72f06-152">Ограничения маршрута</span><span class="sxs-lookup"><span data-stu-id="72f06-152">Route constraints</span></span>

<span data-ttu-id="72f06-153">Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.</span><span class="sxs-lookup"><span data-stu-id="72f06-153">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="72f06-154">В следующем примере маршрут к компоненту `User` соответствует только в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="72f06-154">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="72f06-155">в URL-адресе запроса имеется сегмент маршрута `Id`;</span><span class="sxs-lookup"><span data-stu-id="72f06-155">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="72f06-156">сегмент `Id` имеет целочисленный тип (`int`).</span><span class="sxs-lookup"><span data-stu-id="72f06-156">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="72f06-157">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-157">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="72f06-158">Доступны ограничения маршрутов, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="72f06-158">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="72f06-159">Сведения об ограничениях маршрута, соответствующих инвариантным языку и региональным параметрам, см. в предупреждении внизу таблицы.</span><span class="sxs-lookup"><span data-stu-id="72f06-159">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="72f06-160">Ограничение</span><span class="sxs-lookup"><span data-stu-id="72f06-160">Constraint</span></span> | <span data-ttu-id="72f06-161">Пример</span><span class="sxs-lookup"><span data-stu-id="72f06-161">Example</span></span>           | <span data-ttu-id="72f06-162">Примеры совпадений</span><span class="sxs-lookup"><span data-stu-id="72f06-162">Example Matches</span></span>                                                                  | <span data-ttu-id="72f06-163">Инвариант</span><span class="sxs-lookup"><span data-stu-id="72f06-163">Invariant</span></span><br><span data-ttu-id="72f06-164">язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="72f06-164">culture</span></span><br><span data-ttu-id="72f06-165">соответствие</span><span class="sxs-lookup"><span data-stu-id="72f06-165">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="72f06-166">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="72f06-166">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="72f06-167">Нет</span><span class="sxs-lookup"><span data-stu-id="72f06-167">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="72f06-168">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="72f06-168">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="72f06-169">Да</span><span class="sxs-lookup"><span data-stu-id="72f06-169">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="72f06-170">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="72f06-170">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="72f06-171">Да</span><span class="sxs-lookup"><span data-stu-id="72f06-171">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="72f06-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="72f06-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="72f06-173">Да</span><span class="sxs-lookup"><span data-stu-id="72f06-173">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="72f06-174">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="72f06-174">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="72f06-175">Да</span><span class="sxs-lookup"><span data-stu-id="72f06-175">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="72f06-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="72f06-176">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="72f06-177">Нет</span><span class="sxs-lookup"><span data-stu-id="72f06-177">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="72f06-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="72f06-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="72f06-179">Да</span><span class="sxs-lookup"><span data-stu-id="72f06-179">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="72f06-180">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="72f06-180">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="72f06-181">Да</span><span class="sxs-lookup"><span data-stu-id="72f06-181">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="72f06-182">Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или <xref:System.DateTime>), всегда используют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="72f06-182">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="72f06-183">Эти ограничения предполагают, что URL-адрес является нелокализуемым.</span><span class="sxs-lookup"><span data-stu-id="72f06-183">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="72f06-184">Маршрутизация URL-адресов, содержащих точки</span><span class="sxs-lookup"><span data-stu-id="72f06-184">Routing with URLs that contain dots</span></span>

<span data-ttu-id="72f06-185">Для размещенных приложений Blazor WebAssembly и Blazor Server шаблон маршрута по умолчанию на стороне сервера предполагает, что если последний сегмент URL-адреса запроса содержит точку (`.`), то запрашивается файл.</span><span class="sxs-lookup"><span data-stu-id="72f06-185">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="72f06-186">Например, URL-адрес `https://localhost.com:5001/example/some.thing` интерпретируется маршрутизатором как запрос файла с именем `some.thing`.</span><span class="sxs-lookup"><span data-stu-id="72f06-186">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="72f06-187">Без дополнительной конфигурации приложение возвращает ответ *404 — Not Found* (не найдено), если предполагалась маршрутизация `some.thing` к компоненту с [директивой `@page`](xref:mvc/views/razor#page), а `some.thing` — это значение параметра маршрута.</span><span class="sxs-lookup"><span data-stu-id="72f06-187">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="72f06-188">Чтобы использовать маршрут с одним параметром или несколькими, содержащими точку, в приложении необходимо настроить маршрут с помощью пользовательского шаблона.</span><span class="sxs-lookup"><span data-stu-id="72f06-188">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="72f06-189">Рассмотрим приведенный ниже компонент `Example`, который может получать параметр маршрута из последнего сегмента URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="72f06-189">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="72f06-190">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-190">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="72f06-191">Чтобы позволить приложению *`Server`* размещенного решения Blazor WebAssembly маршрутизировать запрос с точкой в параметре маршрута `param`, добавьте шаблон резервного маршрута к файлу с дополнительным параметром в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72f06-191">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="72f06-192">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="72f06-192">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="72f06-193">Чтобы позволить приложению Blazor Server маршрутизировать запрос с точкой в параметре маршрута `param`, добавьте шаблон резервного маршрута к странице с дополнительным параметром в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72f06-193">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="72f06-194">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="72f06-194">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="72f06-195">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="72f06-195">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="72f06-196">Параметры маршрута catch-all</span><span class="sxs-lookup"><span data-stu-id="72f06-196">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="72f06-197">В компонентах поддерживаются параметры маршрута catch-all, которые захватывают пути в нескольких папках.</span><span class="sxs-lookup"><span data-stu-id="72f06-197">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="72f06-198">Параметры маршрута catch-all</span><span class="sxs-lookup"><span data-stu-id="72f06-198">Catch-all route parameters are:</span></span>

* <span data-ttu-id="72f06-199">Его имя должно соответствовать имени сегмента маршрута.</span><span class="sxs-lookup"><span data-stu-id="72f06-199">Named to match the route segment name.</span></span> <span data-ttu-id="72f06-200">Регистр в имени не учитывается.</span><span class="sxs-lookup"><span data-stu-id="72f06-200">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="72f06-201">Тип `string`.</span><span class="sxs-lookup"><span data-stu-id="72f06-201">A `string` type.</span></span> <span data-ttu-id="72f06-202">Платформа не обеспечивает автоматическое приведение.</span><span class="sxs-lookup"><span data-stu-id="72f06-202">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="72f06-203">В конце URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="72f06-203">At the end of the URL.</span></span>

<span data-ttu-id="72f06-204">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-204">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

<span data-ttu-id="72f06-205">Для URL-адреса `/catch-all/this/is/a/test` с шаблоном маршрута `/catch-all/{*pageRoute}` значение `PageRoute` равно `this/is/a/test`.</span><span class="sxs-lookup"><span data-stu-id="72f06-205">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="72f06-206">Косые черты и сегменты захваченного пути декодированы.</span><span class="sxs-lookup"><span data-stu-id="72f06-206">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="72f06-207">Для шаблона маршрута `/catch-all/{*pageRoute}` URL-адрес `/catch-all/this/is/a%2Ftest%2A` возвращает `this/is/a/test*`.</span><span class="sxs-lookup"><span data-stu-id="72f06-207">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="72f06-208">Параметры маршрута catch-all поддерживаются в ASP.NET Core 5.0 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="72f06-208">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="72f06-209">Для получения дополнительных сведений выберите вариант этой статьи для версии 5.0.</span><span class="sxs-lookup"><span data-stu-id="72f06-209">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="72f06-210">URI и вспомогательные инструменты состояния навигации</span><span class="sxs-lookup"><span data-stu-id="72f06-210">URI and navigation state helpers</span></span>

<span data-ttu-id="72f06-211">Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для управления кодами URI и навигацией в коде C#.</span><span class="sxs-lookup"><span data-stu-id="72f06-211">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="72f06-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> предоставляет события и методы, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="72f06-212"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="72f06-213">Член</span><span class="sxs-lookup"><span data-stu-id="72f06-213">Member</span></span> | <span data-ttu-id="72f06-214">Описание</span><span class="sxs-lookup"><span data-stu-id="72f06-214">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="72f06-215">Возвращает текущий абсолютный URI.</span><span class="sxs-lookup"><span data-stu-id="72f06-215">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="72f06-216">Получает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI.</span><span class="sxs-lookup"><span data-stu-id="72f06-216">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="72f06-217">Как правило, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> соответствует атрибуту `href` элемента документа `<base>` в `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="72f06-217">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="72f06-218">Переходит по указанному URI.</span><span class="sxs-lookup"><span data-stu-id="72f06-218">Navigates to the specified URI.</span></span> <span data-ttu-id="72f06-219">Если значение `forceLoad` равно `true`:</span><span class="sxs-lookup"><span data-stu-id="72f06-219">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="72f06-220">маршрутизация на стороне клиента обходится;</span><span class="sxs-lookup"><span data-stu-id="72f06-220">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="72f06-221">браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</span><span class="sxs-lookup"><span data-stu-id="72f06-221">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="72f06-222">Событие, возникающее при изменении расположения навигации.</span><span class="sxs-lookup"><span data-stu-id="72f06-222">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="72f06-223">Преобразует относительный URI в абсолютный.</span><span class="sxs-lookup"><span data-stu-id="72f06-223">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="72f06-224">Если указан базовый URI (например, URI, возвращенный ранее <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), преобразует абсолютный URI в URI относительно базового префикса.</span><span class="sxs-lookup"><span data-stu-id="72f06-224">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="72f06-225">Для события <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> предоставляет следующие сведения о событиях навигации.</span><span class="sxs-lookup"><span data-stu-id="72f06-225">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="72f06-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>. URL-адрес нового расположения.</span><span class="sxs-lookup"><span data-stu-id="72f06-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="72f06-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>. Если `true`, Blazor перехватывает навигацию из браузера.</span><span class="sxs-lookup"><span data-stu-id="72f06-227"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="72f06-228">Если `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> приводит к переходу.</span><span class="sxs-lookup"><span data-stu-id="72f06-228">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="72f06-229">Приведенный ниже компонент выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="72f06-229">The following component:</span></span>

* <span data-ttu-id="72f06-230">переходит к компоненту `Counter` приложения (`Pages/Counter.razor`) при нажатии кнопки с помощью <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>;</span><span class="sxs-lookup"><span data-stu-id="72f06-230">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="72f06-231">обрабатывает событие изменения расположения путем оформления подписки на <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="72f06-231">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="72f06-232">При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется.</span><span class="sxs-lookup"><span data-stu-id="72f06-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="72f06-233">После отсоединения метода становится возможной сборка мусора для компонента.</span><span class="sxs-lookup"><span data-stu-id="72f06-233">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="72f06-234">При нажатии кнопки реализация средства ведения журнала записывает следующие сведения.</span><span class="sxs-lookup"><span data-stu-id="72f06-234">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="72f06-235">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-235">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

<span data-ttu-id="72f06-236">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="72f06-236">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="72f06-237">Строка запроса и параметры анализа</span><span class="sxs-lookup"><span data-stu-id="72f06-237">Query string and parse parameters</span></span>

<span data-ttu-id="72f06-238">Строка запроса получается из свойства <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="72f06-238">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="72f06-239">Чтобы проанализировать параметры строки запроса:</span><span class="sxs-lookup"><span data-stu-id="72f06-239">To parse a query string's parameters:</span></span>

* <span data-ttu-id="72f06-240">Приложение может использовать API-интерфейс <xref:Microsoft.AspNetCore.WebUtilities>.</span><span class="sxs-lookup"><span data-stu-id="72f06-240">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="72f06-241">Если этот API-интерфейс недоступен приложению, добавьте ссылку на пакет [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) в файл проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="72f06-241">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="72f06-242">Получите значение после анализа строки запроса с помощью <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="72f06-242">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="72f06-243">Приведенный ниже пример компонента `ParseQueryString` анализирует ключ параметра строки запроса с именем `ship`.</span><span class="sxs-lookup"><span data-stu-id="72f06-243">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="72f06-244">Например, для пары "ключ — значение" `?ship=Tardis` в строке запроса URL-адреса записывается значение `Tardis` в `queryValue`.</span><span class="sxs-lookup"><span data-stu-id="72f06-244">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="72f06-245">В приведенном ниже примере происходит переход к приложению по URL-адресу `https://localhost:5001/parse-query-string?ship=Tardis`.</span><span class="sxs-lookup"><span data-stu-id="72f06-245">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="72f06-246">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="72f06-246">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="72f06-247">Новые компоненты `NavLink` и `NavMenu`</span><span class="sxs-lookup"><span data-stu-id="72f06-247">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="72f06-248">Используйте при создании ссылок навигации компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> вместо HTML-элементов гиперссылок (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="72f06-248">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="72f06-249">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="72f06-249">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="72f06-250">Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации.</span><span class="sxs-lookup"><span data-stu-id="72f06-250">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="72f06-251">При необходимости назначьте имя класса CSS свойству <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>, чтобы применить пользовательский класс CSS к отображаемой ссылке, если текущий маршрут совпадает с `href`.</span><span class="sxs-lookup"><span data-stu-id="72f06-251">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="72f06-252">Следующий компонент `NavMenu` создает панель навигации [`Bootstrap`](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:</span><span class="sxs-lookup"><span data-stu-id="72f06-252">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="72f06-253">Компонент `NavMenu` (`NavMenu.razor`) находится в папке `Shared` приложения, созданного на основе шаблонов проектов Blazor.</span><span class="sxs-lookup"><span data-stu-id="72f06-253">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="72f06-254">Существует два параметра <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>, которые можно назначить атрибуту `Match` элемента `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="72f06-254">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="72f06-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует всему текущему URL-адресу;</span><span class="sxs-lookup"><span data-stu-id="72f06-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="72f06-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*по умолчанию*). <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует любому префиксу текущего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="72f06-256"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="72f06-257">В предыдущем примере <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="72f06-257">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="72f06-258">Второй <xref:Microsoft.AspNetCore.Components.Routing.NavLink> получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `component` (например, `https://localhost:5001/component` и `https://localhost:5001/component/another-segment`).</span><span class="sxs-lookup"><span data-stu-id="72f06-258">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="72f06-259">Дополнительные атрибуты компонента <xref:Microsoft.AspNetCore.Components.Routing.NavLink> передаются в отображаемый тег привязки.</span><span class="sxs-lookup"><span data-stu-id="72f06-259">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="72f06-260">В следующем примере компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> включает атрибут `target`.</span><span class="sxs-lookup"><span data-stu-id="72f06-260">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="72f06-261">Отобразится следующая разметка HTML.</span><span class="sxs-lookup"><span data-stu-id="72f06-261">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="72f06-262">В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для рендеринга компонентов `NavLink` в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента `NavLink` используется переменная цикла приращения:</span><span class="sxs-lookup"><span data-stu-id="72f06-262">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="72f06-263">Использование переменной индекса в этом сценарии обязательно для **любого** дочернего компонента, который использует переменную цикла в своем [дочернем содержимом](xref:blazor/components/index#child-content), а не только для компонента `NavLink`.</span><span class="sxs-lookup"><span data-stu-id="72f06-263">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="72f06-264">Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="72f06-264">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="72f06-265">Интеграция маршрутизации конечных точек ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72f06-265">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="72f06-266">*Этот раздел относится только к приложениям Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="72f06-266">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="72f06-267">Blazor Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="72f06-267">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="72f06-268">Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72f06-268">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="72f06-269">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="72f06-269">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="72f06-270">Типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="72f06-270">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="72f06-271">По соглашению страница *узла* обычно называется `_Host.cshtml` и находится в папке `Pages` приложения.</span><span class="sxs-lookup"><span data-stu-id="72f06-271">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="72f06-272">Маршрут, указанный в файле узла, называется *резервным маршрутом*, так как он работает с низким приоритетом в соответствии с правилами маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="72f06-272">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="72f06-273">Резервный маршрут используется, если другие маршруты не сопоставляются.</span><span class="sxs-lookup"><span data-stu-id="72f06-273">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="72f06-274">Это позволяет приложению использовать другие контроллеры и страницы, не мешая маршрутизации компонента в приложении Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="72f06-274">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="72f06-275">Сведения о настройке <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> для размещения сервера по некорневому URL-адресу см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="72f06-275">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
