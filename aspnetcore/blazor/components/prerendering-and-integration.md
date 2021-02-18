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
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a>Компоненты Razor для предварительной визуализации и интеграции ASP.NET Core

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Компоненты Razor можно интегрировать в приложения Razor Pages и MVC в размещенном решении Blazor WebAssembly. Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.

## <a name="configuration"></a>Параметр Configuration

Чтобы настроить предварительную визуализацию для приложения Blazor WebAssembly, сделайте следующее:

1. Разместите приложение Blazor WebAssembly в приложении ASP.NET Core. Вы можете добавить изолированное приложение Blazor WebAssembly в решение ASP.NET Core или использовать размещенное приложение Blazor WebAssembly, созданное из шаблона проекта Blazor Hosted.

1. Удалите стандартный статический файл `wwwroot/index.html` из проекта клиента Blazor WebAssembly.

1. Удалите следующую строку в `Program.Main` в клиентском проекте:

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. Добавьте файл `Pages/_Host.cshtml` в серверный проект. Файл `_Host.cshtml` можно получить из приложения, созданного из шаблона Blazor Server, используя в командной оболочке команду `dotnet new blazorserver -o BlazorServer`. После помещения файла `Pages/_Host.cshtml` в серверное приложение размещенного решения Blazor WebAssembly внесите следующие изменения в файл:

   * Задайте для пространства имен папку `Pages` серверного приложения (например, `@namespace BlazorHosted.Server.Pages`).
   * Задайте директиву [`@using`](xref:mvc/views/razor#using) для клиентского проекта (например, `@using BlazorHosted.Client`).
   * Обновите ссылки таблицы стилей, чтобы они указывали на таблицу стилей приложения WebAssembly. В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * Обновите `render-mode` [вспомогательной функции тегов компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper), чтобы выполнить предварительную визуализацию для корневого компонента `App`:

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Обновите источник скрипта Blazor, чтобы использовать скрипт Blazor WebAssembly на стороне клиента:

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. В `Startup.Configure` (`Startup.cs`) серверного проекта:

   * Вызовите `UseDeveloperExceptionPage` в конструкторе приложений в среде разработки.
   * Вызовите `UseBlazorFrameworkFiles` в конструкторе приложений.
   * Измените значение отката со страницы `index.html` (`endpoints.MapFallbackToFile("index.html");`) на страницу `_Host.cshtml`.

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

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Преобразование для просмотра компонентов на странице или в представлении с помощью вспомогательной функции тегов компонента

Вспомогательная функция тегов компонента поддерживает два режима отрисовки компонента из приложения Blazor WebAssembly на странице или в представлении:

* `WebAssembly`: Преобразовывает маркер для приложения Blazor WebAssembly, которое используется для включения интерактивного компонента при загрузке в браузере. Компонент предварительно не визуализирован. Этот параметр упрощает преобразование различных компонентов Blazor WebAssembly на разных страницах.
* `WebAssemblyPrerendered`: Предварительно визуализирует компонент в статический HTML и включает маркер для приложения Blazor WebAssembly, который впоследствии будет использован, чтобы сделать компонент интерактивным при загрузке в браузере.

В следующем примере Razor Pages компонент `Counter` отрисовывается на странице. Чтобы сделать компонент интерактивным, сценарий Blazor WebAssembly добавляется в раздел [отображения](xref:mvc/views/layout#sections) страницы. Чтобы избежать использования полного пространства имен для компонента `Counter` с вспомогательной функцией тегов компонента (`{APP ASSEMBLY}.Pages.Counter`), добавьте директиву [`@using`](xref:mvc/views/razor#using) для пространства имен `Pages` приложения клиента. В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента:

* компонент предварительно преобразуется в страницу;
* компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

В предыдущем примере требуется, чтобы макет серверного приложения (`_Layout.cshtml`) содержал [раздел преобразования для просмотра](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) для скрипта в закрывающем теге `</body>`:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.

Если приложение также должно иметь стиль компонентов со стилями в приложении Blazor WebAssembly, добавьте стили приложения в файл `_Layout.cshtml`. В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Преобразование компонентов на странице или в представлении с помощью селектора CSS

Добавьте корневые компоненты в проект *Client* в `Program.Main` (`Program.cs`). В следующем примере компонент `Counter` объявляется как корневой компонент с помощью селектора CSS, который выбирает элемент с `id`, соответствующим `my-counter`. В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

В следующем примере Razor Pages компонент `Counter` отрисовывается на странице. Чтобы сделать компонент интерактивным, сценарий Blazor WebAssembly добавляется в раздел [отображения](xref:mvc/views/layout#sections) страницы:

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

В предыдущем примере требуется, чтобы макет серверного приложения (`_Layout.cshtml`) содержал [раздел преобразования для просмотра](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) для скрипта в закрывающем теге `</body>`:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.

Если приложение также должно иметь стиль компонентов со стилями в приложении Blazor WebAssembly, добавьте стили приложения в файл `_Layout.cshtml`. В следующем примере пространством имен клиентского приложения является `BlazorHosted.Client`:

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Интеграция компонентов Razor в приложения Razor Pages и MVC в размещенном решении Blazor WebAssembly поддерживается в ASP.NET Core в .NET 5 или более поздней версии. Выберите вариант этой статьи для версии .NET 5 или более поздней.

::: moniker-end

::: zone-end

::: zone pivot="server"

Компоненты Razor можно интегрировать в приложения Razor Pages и MVC в приложении Blazor Server. Одновременно с отрисовкой страницы или представления можно выполнять предварительную обработку компонентов.

[Настроив приложение](#configuration), следуйте рекомендациям в следующих разделах в зависимости от требований приложения:

* Маршрутизируемые компоненты. Для компонентов, напрямую маршрутизируемых из запросов пользователей. Следуйте этому руководству, когда посетители должны иметь возможность сделать HTTP-запрос в браузере для компонента с директивой [`@page`](xref:mvc/views/razor#page).
  * [Использование маршрутизируемых компонентов в приложении Razor Pages](#use-routable-components-in-a-razor-pages-app)
  * [Использование маршрутизируемых компонентов в приложении MVC](#use-routable-components-in-an-mvc-app)
* [Отрисовка компонентов со страницы или представления.](#render-components-from-a-page-or-view) Для компонентов, которые не маршрутизируются напрямую из запросов пользователей. Следуйте этому руководству, когда приложение внедряет компоненты в существующие страницы и представления с помощью вспомогательной функции [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="configuration"></a>Параметр Configuration

Существующее приложение MVC или Razor Pages может интегрировать компоненты Razor в страницы и представления:

1. В файле макета приложения (`_Layout.cshtml`):

   * Добавьте следующий тег `<base>` в элемент `<head>`:

     ```html
     <base href="~/" />
     ```

     Значение `href` (*базовый путь к приложению* ) в предыдущем примере предполагает, что приложение находится по корневому URL-пути (`/`). Если приложение является подчиненным, следуйте инструкциям в разделе *Базовый путь к приложению* статьи <xref:blazor/host-and-deploy/index#app-base-path>.

     Файл `_Layout.cshtml` находится в папке `Pages/Shared` приложения Razor Pages или в папке `Views/Shared` приложения MVC.

   * Добавьте тег `<script>` для скрипта `blazor.server.js` непосредственно перед разделом преобразования `Scripts`:

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     Платформа добавляет скрипт `blazor.server.js` в приложение. Добавлять скрипт в приложение вручную не требуется.

1. Добавьте файл `_Imports.razor` в корневую папку проекта со следующим содержимым (измените последнее пространство имен `MyAppNamespace` на пространство имен приложения):

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

1. Зарегистрируйте службу Blazor Server в `Startup.ConfigureServices`:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. В `Startup.Configure` добавьте конечную точку Blazor Hub в `app.UseEndpoints`.

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Интегрируйте компоненты в какую-либо страницу или какое-либо представление. Дополнительные сведения см. в разделе [Отрисовка компонентов со страницы или представления](#render-components-from-a-page-or-view).

## <a name="use-routable-components-in-a-razor-pages-app"></a>Использование маршрутизируемых компонентов в приложении Razor Pages

*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*

Чтобы обеспечить поддержку маршрутизируемых компонентов Razor в приложениях Razor Pages:

1. Следуйте указаниям в разделе [Конфигурация](#configuration).

1. Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:

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

1. Добавьте файл `_Host.cshtml` в папку `Pages` со следующим содержимым:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Для макета компоненты используют общий файл `_Layout.cshtml`.

   Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:

   * компонент предварительно преобразуется в страницу;
   * компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

   Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Добавьте маршрут с низким приоритетом для страницы `_Host.cshtml` в конфигурацию конечной точки в `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Добавьте маршрутизируемые компоненты в приложение. Пример:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).

## <a name="use-routable-components-in-an-mvc-app"></a>Использование маршрутизируемых компонентов в приложении MVC

*Этот раздел описывает добавление компонентов, напрямую маршрутизируемых из запросов пользователей.*

Для поддержки маршрутизируемых компонентов Razor в приложениях MVC сделайте следующее:

1. Следуйте указаниям в разделе [Конфигурация](#configuration).

1. Добавьте файл `App.razor` в корневой каталог проекта со следующим содержимым:

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

1. Добавьте файл `_Host.cshtml` в папку `Views/Home` со следующим содержимым:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Для макета компоненты используют общий файл `_Layout.cshtml`.

   Параметр <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> настраивает одно из следующих поведений компонента `App`:

   * компонент предварительно преобразуется в страницу;
   * компонент отображается как статический HTML на странице или включает необходимые сведения для начальной загрузки приложения Blazor из агента пользователя.

   Дополнительные сведения о вспомогательной функции тегов компонента, в том числе о передаче параметров и конфигурации <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, см. в статье <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Добавьте действие в контроллер Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Добавьте маршрут с низким приоритетом для действия контроллера, которое возвращает представление `_Host.cshtml`, в конфигурацию конечной точки в `Startup.Configure`:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Создайте папку `Pages` и добавьте маршрутизируемые компоненты в приложение. Пример:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Дополнительные сведения о пространствах имен см. в разделе [Пространства имен компонентов](#component-namespaces).

## <a name="render-components-from-a-page-or-view"></a>Отрисовка компонентов со страницы или представления

*Этот раздел описывает добавление на страницы или в представления компонентов, не являющихся напрямую маршрутизируемыми из запросов пользователей.*

Чтобы отрисовать компонент из страницы или представления, используйте [вспомогательную функцию тега компонента](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Отрисовка интерактивных компонентов с отслеживанием состояния

На страницу или в представление Razor можно добавить интерактивные компоненты с отслеживанием состояния.

При отображении страницы или представления:

* компонент предварительно отображается страницей или представлением;
* исходное состояние компонента, используемое для предварительной визуализации, теряется;
* новое состояние компонента создается при установке подключения SignalR.

Следующая страница Razor визуализирует компонент `Counter`.

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Отрисовка неинтерактивных компонентов

На следующей странице Razor компонент `Counter` статически подготавливается к просмотру с начальным значением, указанным с помощью формы. Так как этот компонент отображается статически, он не может быть интерактивным:

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

Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Пространства имен компонентов

При использовании настраиваемой папки для хранения компонентов приложения добавьте пространство имен, представляющее эту папку, на страницу или в представление либо в файл `_ViewImports.cshtml`. В следующем примере:

* Измените `MyAppNamespace` на пространство имен приложения.
* Если папка с именем `Components` не используется для хранения компонентов, измените `Components` на папку, где находятся компоненты.

```cshtml
@using MyAppNamespace.Components
```

Файл `_ViewImports.cshtml` находится в папке `Pages` приложения Razor Pages или в папке `Views` приложения MVC.

Для получения дополнительной информации см. <xref:blazor/components/index#namespaces>.

::: zone-end
