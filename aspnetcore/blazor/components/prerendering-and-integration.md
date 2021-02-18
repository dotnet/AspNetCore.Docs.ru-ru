---
title: Компоненты Razor для предварительной визуализации и интеграции ASP.NET Core
author: guardrex
description: Сведения о сценариях интеграции компонентов Razor для приложений Blazor, в том числе о предварительной обработке компонентов Razor на сервере.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: d120440c292d15b7741260ed31af92d60db2261c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280066"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="ae322-103">Компоненты Razor для предварительной визуализации и интеграции ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae322-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ae322-104">Компоненты Razor можно интегрировать в приложения Razor Pages и MVC в размещенном решении Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ae322-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="ae322-105">Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.</span><span class="sxs-lookup"><span data-stu-id="ae322-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="ae322-106">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="ae322-106">Configuration</span></span>

<span data-ttu-id="ae322-107">Чтобы настроить предварительную визуализацию для приложения Blazor WebAssembly, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ae322-107">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="ae322-108">Разместите приложение Blazor WebAssembly в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ae322-108">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="ae322-109">Вы можете добавить изолированное приложение Blazor WebAssembly в решение ASP.NET Core или использовать размещенное приложение Blazor WebAssembly, созданное из шаблона проекта Blazor Hosted.</span><span class="sxs-lookup"><span data-stu-id="ae322-109">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="ae322-110">Удалите стандартный статический файл `wwwroot/index.html` из проекта клиента Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ae322-110">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="ae322-111">Удалите следующую строку в `Program.Main` в клиентском проекте:</span><span class="sxs-lookup"><span data-stu-id="ae322-111">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="ae322-112">Добавьте файл `Pages/_Host.cshtml` в серверный проект.</span><span class="sxs-lookup"><span data-stu-id="ae322-112">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="ae322-113">Файл `_Host.cshtml` можно получить из приложения, созданного из шаблона Blazor Server, используя в командной оболочке команду `dotnet new blazorserver -o BlazorServer`.</span><span class="sxs-lookup"><span data-stu-id="ae322-113">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="ae322-114">После помещения файла `Pages/_Host.cshtml` в серверное приложение размещенного решения Blazor WebAssembly внесите следующие изменения в файл:</span><span class="sxs-lookup"><span data-stu-id="ae322-114">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="ae322-115">Задайте для пространства имен папку `Pages` серверного приложения (например, `@namespace BlazorHosted.Server.Pages`).</span><span class="sxs-lookup"><span data-stu-id="ae322-115">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="ae322-116">Задайте директиву [`@using`](xref:mvc/views/razor#using) для клиентского проекта (например, `@using BlazorHosted.Client`).</span><span class="sxs-lookup"><span data-stu-id="ae322-116">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="ae322-117">Обновите ссылки таблицы стилей, чтобы они указывали на таблицу стилей приложения WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ae322-117">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="ae322-118">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="ae322-118">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="ae322-119">Обновите `render-mode` [вспомогательной функции тегов компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper), чтобы выполнить предварительную визуализацию для корневого компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="ae322-119">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="ae322-120">Обновите источник скрипта Blazor, чтобы использовать скрипт Blazor WebAssembly на стороне клиента:</span><span class="sxs-lookup"><span data-stu-id="ae322-120">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="ae322-121">В `Startup.Configure` (`Startup.cs`) серверного проекта:</span><span class="sxs-lookup"><span data-stu-id="ae322-121">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="ae322-122">Вызовите `UseDeveloperExceptionPage` в конструкторе приложений в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="ae322-122">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="ae322-123">Вызовите `UseBlazorFrameworkFiles` в конструкторе приложений.</span><span class="sxs-lookup"><span data-stu-id="ae322-123">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="ae322-124">Измените значение отката со страницы `index.html` (`endpoints.MapFallbackToFile("index.html");`) на страницу `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ae322-124">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="ae322-125">Преобразование для просмотра компонентов на странице или в представлении с помощью вспомогательной функции тегов компонента</span><span class="sxs-lookup"><span data-stu-id="ae322-125">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="ae322-126">Вспомогательная функция тегов компонента поддерживает два режима отрисовки компонента из приложения Blazor WebAssembly на странице или в представлении:</span><span class="sxs-lookup"><span data-stu-id="ae322-126">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="ae322-127">`WebAssembly`: Преобразовывает маркер для приложения Blazor WebAssembly, которое используется для включения интерактивного компонента при загрузке в браузере.</span><span class="sxs-lookup"><span data-stu-id="ae322-127">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="ae322-128">Компонент предварительно не визуализирован.</span><span class="sxs-lookup"><span data-stu-id="ae322-128">The component isn't prerendered.</span></span> <span data-ttu-id="ae322-129">Этот параметр упрощает преобразование различных компонентов Blazor WebAssembly на разных страницах.</span><span class="sxs-lookup"><span data-stu-id="ae322-129">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="ae322-130">`WebAssemblyPrerendered`: Предварительно визуализирует компонент в статический HTML и включает маркер для приложения Blazor WebAssembly, который впоследствии будет использован, чтобы сделать компонент интерактивным при загрузке в браузере.</span><span class="sxs-lookup"><span data-stu-id="ae322-130">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="ae322-131">В следующем примере Razor Pages компонент `Counter` отрисовывается на странице.</span><span class="sxs-lookup"><span data-stu-id="ae322-131">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="ae322-132">Чтобы сделать компонент интерактивным, сценарий Blazor WebAssembly добавляется в раздел [отображения](xref:mvc/views/layout#sections) страницы.</span><span class="sxs-lookup"><span data-stu-id="ae322-132">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="ae322-133">Чтобы избежать использования полного пространства имен для компонента `Counter` с вспомогательной функцией тегов компонента (`{APP ASSEMBLY}.Pages.Counter`), добавьте директиву [`@using`](xref:mvc/views/razor#using) для пространства имен `Pages` приложения клиента.</span><span class="sxs-lookup"><span data-stu-id="ae322-133">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="ae322-134">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="ae322-134">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="ae322-135">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента:</span><span class="sxs-lookup"><span data-stu-id="ae322-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="ae322-136">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="ae322-136">Is prerendered into the page.</span></span>
* <span data-ttu-id="ae322-137">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="ae322-137">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="ae322-138">Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ae322-138">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="ae322-139">В предыдущем примере требуется, чтобы макет серверного приложения (`_Layout.cshtml`) содержал [раздел преобразования для просмотра](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) для скрипта в закрывающем теге `</body>`:</span><span class="sxs-lookup"><span data-stu-id="ae322-139">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="ae322-140">Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="ae322-140">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="ae322-141">Если приложение также должно иметь стиль компонентов со стилями в приложении Blazor WebAssembly, добавьте стили приложения в файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ae322-141">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="ae322-142">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="ae322-142">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="ae322-143">Преобразование компонентов на странице или в представлении с помощью селектора CSS</span><span class="sxs-lookup"><span data-stu-id="ae322-143">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="ae322-144">Добавьте корневые компоненты в проект *Client* в `Program.Main` (`Program.cs`).</span><span class="sxs-lookup"><span data-stu-id="ae322-144">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="ae322-145">В следующем примере компонент `Counter` объявляется как корневой компонент с помощью селектора CSS, который выбирает элемент с `id`, соответствующим `my-counter`.</span><span class="sxs-lookup"><span data-stu-id="ae322-145">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="ae322-146">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="ae322-146">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="ae322-147">В следующем примере Razor Pages компонент `Counter` отрисовывается на странице.</span><span class="sxs-lookup"><span data-stu-id="ae322-147">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="ae322-148">Чтобы сделать компонент интерактивным, сценарий Blazor WebAssembly добавляется в раздел [отображения](xref:mvc/views/layout#sections) страницы:</span><span class="sxs-lookup"><span data-stu-id="ae322-148">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="ae322-149">В предыдущем примере требуется, чтобы макет серверного приложения (`_Layout.cshtml`) содержал [раздел преобразования для просмотра](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) для скрипта в закрывающем теге `</body>`:</span><span class="sxs-lookup"><span data-stu-id="ae322-149">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="ae322-150">Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="ae322-150">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="ae322-151">Если приложение также должно иметь стиль компонентов со стилями в приложении Blazor WebAssembly, добавьте стили приложения в файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ae322-151">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="ae322-152">В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="ae322-152">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ae322-153">Интеграция компонентов Razor в приложения Razor Pages и MVC в размещенном решении Blazor WebAssembly поддерживается в ASP.NET Core в .NET 5 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="ae322-153">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="ae322-154">Выберите вариант этой статьи для версии .NET 5 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="ae322-154">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="ae322-155">Компоненты Razor можно интегрировать в приложения Razor Pages и MVC в приложении Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ae322-155">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="ae322-156">Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.</span><span class="sxs-lookup"><span data-stu-id="ae322-156">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="ae322-157">[Настроив приложение](#configuration), следуйте рекомендациям в следующих разделах в зависимости от требований приложения:</span><span class="sxs-lookup"><span data-stu-id="ae322-157">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="ae322-158">Маршрутизируемые компоненты. Для компонентов, напрямую маршрутизируемых из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="ae322-158">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="ae322-159">Следуйте этому руководству, когда посетители должны иметь возможность сделать HTTP-запрос в браузере для компонента с директивой [`@page`](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="ae322-159">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="ae322-160">Использование маршрутизируемых компонентов в приложении Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ae322-160">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="ae322-161">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="ae322-161">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="ae322-162">[Отрисовка компонентов со страницы или представления.](#render-components-from-a-page-or-view) Для компонентов, которые не маршрутизируются напрямую из запросов пользователей.</span><span class="sxs-lookup"><span data-stu-id="ae322-162">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="ae322-163">Следуйте этому руководству, когда приложение внедряет компоненты в существующие страницы и представления с помощью вспомогательной функции [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ae322-163">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="ae322-164">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="ae322-164">Configuration</span></span>

<span data-ttu-id="ae322-165">Существующее приложение MVC или Razor Pages может интегрировать компоненты Razor в страницы и представления:</span><span class="sxs-lookup"><span data-stu-id="ae322-165">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="ae322-166">В файле макета приложения (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="ae322-166">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="ae322-167">Добавьте следующий тег `<base>` в элемент `<head>`:</span><span class="sxs-lookup"><span data-stu-id="ae322-167">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="ae322-168">Значение `href` (*базовый путь к приложению* ) в предыдущем примере предполагает, что приложение находится по корневому URL-пути (`/`).</span><span class="sxs-lookup"><span data-stu-id="ae322-168">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="ae322-169">Если приложение является подчиненным, следуйте инструкциям в разделе *Базовый путь к приложению* статьи <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="ae322-169">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="ae322-170">Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="ae322-170">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="ae322-171">Добавьте тег `<script>` для скрипта `blazor.server.js` непосредственно перед разделом преобразования `Scripts`:</span><span class="sxs-lookup"><span data-stu-id="ae322-171">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="ae322-172">Платформа добавляет скрипт `blazor.server.js` в приложение.</span><span class="sxs-lookup"><span data-stu-id="ae322-172">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="ae322-173">Добавлять скрипт в приложение вручную не требуется.</span><span class="sxs-lookup"><span data-stu-id="ae322-173">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="ae322-174">Добавьте файл `_Imports.razor` в корневую папку проекта со следующим содержимым (измените последнее пространство имен `MyAppNamespace` на пространство имен приложения):</span><span class="sxs-lookup"><span data-stu-id="ae322-174">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="ae322-175">Зарегистрируйте службу Blazor Server в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ae322-175">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="ae322-176">В `Startup.Configure` добавьте конечную точку Blazor Hub в `app.UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="ae322-176">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="ae322-177">Интегрируйте компоненты в какую-либо страницу или какое-либо представление.</span><span class="sxs-lookup"><span data-stu-id="ae322-177">Integrate components into any page or view.</span></span> <span data-ttu-id="ae322-178">Дополнительные сведения см. в разделе [Отрисовка компонентов со страницы или представления](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="ae322-178">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="ae322-179">Использование маршрутизируемых компонентов в приложении Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ae322-179">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="ae322-180">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="ae322-180">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ae322-181">Чтобы обеспечить поддержку маршрутизируемых компонентов Razor в приложениях Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="ae322-181">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="ae322-182">Следуйте указаниям в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="ae322-182">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="ae322-183">Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="ae322-183">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="ae322-184">Добавьте файл `_Host.cshtml` в папку `Pages` со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="ae322-184">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ae322-185">Для макета компоненты используют общий файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ae322-185">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="ae322-186">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="ae322-186"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="ae322-187">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="ae322-187">Is prerendered into the page.</span></span>
   * <span data-ttu-id="ae322-188">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="ae322-188">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="ae322-189">Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ae322-189">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="ae322-190">Добавьте маршрут с низким приоритетом для страницы `_Host.cshtml` в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ae322-190">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="ae322-191">Добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="ae322-191">Add routable components to the app.</span></span> <span data-ttu-id="ae322-192">Пример:</span><span class="sxs-lookup"><span data-stu-id="ae322-192">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="ae322-193">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="ae322-193">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="ae322-194">Использование маршрутизируемых компонентов в приложении MVC</span><span class="sxs-lookup"><span data-stu-id="ae322-194">Use routable components in an MVC app</span></span>

<span data-ttu-id="ae322-195">*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="ae322-195">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ae322-196">Для поддержки маршрутизируемых компонентов Razor в приложениях MVC сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="ae322-196">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="ae322-197">Следуйте указаниям в разделе [Конфигурация](#configuration).</span><span class="sxs-lookup"><span data-stu-id="ae322-197">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="ae322-198">Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="ae322-198">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="ae322-199">Добавьте файл `_Host.cshtml` в папку `Views/Home` со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="ae322-199">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ae322-200">Для макета компоненты используют общий файл `_Layout.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ae322-200">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="ae322-201">Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:</span><span class="sxs-lookup"><span data-stu-id="ae322-201"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="ae322-202">компонент предварительно преобразуется в страницу;</span><span class="sxs-lookup"><span data-stu-id="ae322-202">Is prerendered into the page.</span></span>
   * <span data-ttu-id="ae322-203">компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="ae322-203">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="ae322-204">Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ae322-204">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="ae322-205">Добавьте действие в контроллер Home:</span><span class="sxs-lookup"><span data-stu-id="ae322-205">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="ae322-206">Добавьте маршрут с низким приоритетом для действия контроллера, которое возвращает представление `_Host.cshtml`, в конфигурацию конечной точки в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ae322-206">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="ae322-207">Создайте папку `Pages` и добавьте маршрутизируемые компоненты в приложение.</span><span class="sxs-lookup"><span data-stu-id="ae322-207">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="ae322-208">Пример:</span><span class="sxs-lookup"><span data-stu-id="ae322-208">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="ae322-209">Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="ae322-209">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="ae322-210">Отрисовка компонентов со страницы или представления</span><span class="sxs-lookup"><span data-stu-id="ae322-210">Render components from a page or view</span></span>

<span data-ttu-id="ae322-211">*Этот раздел описывает добавление на страницы или в представления компонентов, не являющихся напрямую маршрутизируемыми из запросов пользователей.*</span><span class="sxs-lookup"><span data-stu-id="ae322-211">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="ae322-212">Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ae322-212">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="ae322-213">Отрисовка интерактивных компонентов с отслеживанием состояния</span><span class="sxs-lookup"><span data-stu-id="ae322-213">Render stateful interactive components</span></span>

<span data-ttu-id="ae322-214">На страницу или в представление Razor можно добавить интерактивные компоненты с отслеживанием состояния.</span><span class="sxs-lookup"><span data-stu-id="ae322-214">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="ae322-215">При отображении страницы или представления:</span><span class="sxs-lookup"><span data-stu-id="ae322-215">When the page or view renders:</span></span>

* <span data-ttu-id="ae322-216">компонент предварительно отображается страницей или представлением;</span><span class="sxs-lookup"><span data-stu-id="ae322-216">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="ae322-217">исходное состояние компонента, используемое для предварительной визуализации, теряется;</span><span class="sxs-lookup"><span data-stu-id="ae322-217">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="ae322-218">новое состояние компонента создается при установке подключения SignalR.</span><span class="sxs-lookup"><span data-stu-id="ae322-218">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="ae322-219">Следующая страница Razor визуализирует компонент `Counter`.</span><span class="sxs-lookup"><span data-stu-id="ae322-219">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="ae322-220">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ae322-220">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="ae322-221">Отрисовка неинтерактивных компонентов</span><span class="sxs-lookup"><span data-stu-id="ae322-221">Render noninteractive components</span></span>

<span data-ttu-id="ae322-222">На следующей странице Razor компонент `Counter` статически подготавливается к просмотру с начальным значением, указанным с помощью формы.</span><span class="sxs-lookup"><span data-stu-id="ae322-222">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="ae322-223">Так как этот компонент отображается статически, он не может быть интерактивным:</span><span class="sxs-lookup"><span data-stu-id="ae322-223">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="ae322-224">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ae322-224">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="ae322-225">Пространства имен компонентов</span><span class="sxs-lookup"><span data-stu-id="ae322-225">Component namespaces</span></span>

<span data-ttu-id="ae322-226">При использовании настраиваемой папки для хранения компонентов приложения добавьте пространство имен, представляющее эту папку, на страницу или в представление либо в файл `_ViewImports.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="ae322-226">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="ae322-227">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ae322-227">In the following example:</span></span>

* <span data-ttu-id="ae322-228">Измените `MyAppNamespace` на пространство имен приложения.</span><span class="sxs-lookup"><span data-stu-id="ae322-228">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="ae322-229">Если папка с именем `Components` не используется для хранения компонентов, измените `Components` на папку, где находятся компоненты.</span><span class="sxs-lookup"><span data-stu-id="ae322-229">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="ae322-230">Файл `_ViewImports.cshtml` находится в папке `Pages` приложения Razor Pages или в папке `Views` приложения MVC.</span><span class="sxs-lookup"><span data-stu-id="ae322-230">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="ae322-231">Для получения дополнительной информации см. <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="ae322-231">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
