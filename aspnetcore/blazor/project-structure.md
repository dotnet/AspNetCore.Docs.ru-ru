---
title: Структура проекта ASP.NET Core Blazor
author: guardrex
description: Сведения о структуре проекта приложения Blazor в ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: 958fa23a1befac3696d850d5409d4021dd109c22
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751546"
---
# <a name="aspnet-core-blazor-project-structure"></a>Структура проекта ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

В этой статье описываются файлы и папки, из которых состоит приложение Blazor, созданное на основе шаблонов проектов Blazor.

## Blazor WebAssembly

Шаблон Blazor WebAssembly (`blazorwasm`) создает исходные файлы и структуру каталогов для приложения Blazor WebAssembly. Приложение заполняется демонстрационным кодом для компонента `FetchData`, который загружает данные из статического ресурса `weather.json`, обеспечивая взаимодействие пользователя с компонентом `Counter`.

* Папка `Pages`: содержит маршрутизируемые компоненты или страницы (`.razor`), которые составляют приложение Blazor. Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page). Шаблон включает в себя следующие компоненты:
  * Компонент `Counter` (`Counter.razor`): Реализует страницу счетчика.
  * Компонент `FetchData` (`FetchData.razor`): Реализует страницу получения данных.
  * Компонент `Index` (`Index.razor`): Реализует главную страницу приложения.
  
* `Properties/launchSettings.json`. Содержит [конфигурацию среды разработки](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* Папка `Shared`: содержит следующие общие компоненты и таблицы стилей:
  * Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.
  * `MainLayout.razor.css`: Таблица стилей для основного макета приложения.
  * Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации. Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.
  * `NavMenu.razor.css`: Таблица стилей для меню навигации приложения.
  * Компонент `SurveyPrompt` (`SurveyPrompt.razor`): компонент опроса Blazor.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Папка `Shared`: содержит следующие общие компоненты:
  * Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.
  * Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации. Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.
  * Компонент `SurveyPrompt` (`SurveyPrompt.razor`): компонент опроса Blazor.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: папка [Web Root](xref:fundamentals/index#web-root) для приложения, содержащего общедоступные статические ресурсы приложения, включая `appsettings.json` и файлы параметров приложения среды для [параметров конфигурации](xref:blazor/fundamentals/configuration). Веб-страница `index.html` — это корневая страница приложения, реализованная в виде HTML-страницы:
  * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
  * На странице указывается место отрисовки корневого компонента `App`. Компонент отображается в расположении элемента модели DOM `div` с `id` `app` (`<div id="app">Loading...</div>`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: папка [Web Root](xref:fundamentals/index#web-root) для приложения, содержащего общедоступные статические ресурсы приложения, включая `appsettings.json` и файлы параметров приложения среды для [параметров конфигурации](xref:blazor/fundamentals/configuration). Веб-страница `index.html` — это корневая страница приложения, реализованная в виде HTML-страницы:
  * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
  * На странице указывается место отрисовки корневого компонента `App`. Компонент отображается в расположении элемента модели DOM `app` (`<app>Loading...</app>`).

::: moniker-end

> [!NOTE]
> Файлы JavaScript (JS), добавленные в файл `wwwroot/index.html`, должны отображаться перед закрывающим тегом `</body>`. Порядок загрузки пользовательского кода JS из JS-файлов важен в некоторых сценариях. Например, убедитесь, что JS-файлы с методами взаимодействия JS включены перед JS-файлами платформы Blazor.

* `_Imports.razor`. Содержит стандартные директивы Razor, включаемые в компоненты приложения (`.razor`), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.

* `App.razor`. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>. Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: это точка входа в приложение, где настраиваются следующие компоненты узла WebAssembly:
  
  * Компонент `App` является корневым компонентом приложения. Компонент `App` указывается как элемент модели DOM `div` с `id` `app` (`<div id="app">Loading...</div>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("#app")`).
  * [Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: это точка входа в приложение, где настраиваются следующие компоненты узла WebAssembly:

  * Компонент `App` является корневым компонентом приложения. Компонент `App` указывается как элемент модели DOM `app` (`<app>Loading...</app>` в `wwwroot/index.html`) в корневой коллекции компонентов (`builder.RootComponents.Add<App>("app")`).
  * [Службы](xref:blazor/fundamentals/dependency-injection) добавляются и настраиваются (например, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

## Blazor Server

Шаблон Blazor Server (`blazorserver`) создает исходные файлы и структуру каталогов для приложения Blazor Server. Приложение заполняется демонстрационным кодом для компонента `FetchData`, который загружает данные из зарегистрированной службы `WeatherForecastService`, обеспечивая взаимодействие пользователя с компонентом `Counter`.

* Папка `Data`: содержит класс `WeatherForecast` и реализацию `WeatherForecastService`, которые предоставляют пример метеоданных для компонента `FetchData` приложения.

* Папка `Pages`: содержит маршрутизируемые компоненты и страницы (`.razor`), которые входят в приложение Blazor, а также корневую страницу Razor приложения Blazor Server. Маршрут для каждой страницы указывается с помощью директивы [`@page`](xref:mvc/views/razor#page). Шаблон включает в себя следующее:
  * `_Host.cshtml`: Корневая страница приложения, реализованная как страница Razor.
    * При первом запросе любой страницы приложения эта страница преобразовывается для просмотра и возвращается в ответе.
    * На странице Host указывается место отрисовки корневого компонента `App` (`App.razor`).
  * Компонент `Counter` (`Counter.razor`): Реализует страницу счетчика.
  * Компонент `Error` (`Error.razor`): Отображается, когда в приложении происходит необработанное исключение.
  * Компонент `FetchData` (`FetchData.razor`): Реализует страницу получения данных.
  * Компонент `Index` (`Index.razor`): Реализует главную страницу приложения.

> [!NOTE]
> Файлы JavaScript (JS), добавленные в файл `Pages/_Host.cshtml`, должны отображаться перед закрывающим тегом `</body>`. Порядок загрузки пользовательского кода JS из JS-файлов важен в некоторых сценариях. Например, убедитесь, что JS-файлы с методами взаимодействия JS включены перед JS-файлами платформы Blazor.

* `Properties/launchSettings.json`. Содержит [конфигурацию среды разработки](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* Папка `Shared`: содержит следующие общие компоненты и таблицы стилей:
  * Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.
  * `MainLayout.razor.css`: Таблица стилей для основного макета приложения.
  * Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации. Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.
  * `NavMenu.razor.css`: Таблица стилей для меню навигации приложения.
  * Компонент `SurveyPrompt` (`SurveyPrompt.razor`): компонент опроса Blazor.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Папка `Shared`: содержит следующие общие компоненты:
  * Компонент `MainLayout` (`MainLayout.razor`): [Компонент макета](xref:blazor/layouts) приложения.
  * Компонент `NavMenu` (`NavMenu.razor`): Реализует боковую панель навигации. Включает в себя [компонент `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), который служит для отрисовки навигационных ссылок на другие компоненты Razor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> автоматически указывает выбранное состояние при загрузке компонента, что помогает пользователю понять, какой компонент отображается в настоящее время.
  * Компонент `SurveyPrompt` (`SurveyPrompt.razor`): компонент опроса Blazor.
  
::: moniker-end

* `wwwroot`. Папка [корневого каталога документов](xref:fundamentals/index#web-root) для приложения, которая содержит общедоступные статические ресурсы.

* `_Imports.razor`. Содержит стандартные директивы Razor, включаемые в компоненты приложения (`.razor`), например директивы [`@using`](xref:mvc/views/razor#using) для пространств имен.

* `App.razor`. Корневой компонент приложения, который настраивает маршрутизацию на стороне клиента с помощью компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>. Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> перехватывает навигацию в браузере и отображает страницу, соответствующую запрошенному адресу.

* `appsettings.json` и файлы параметров приложения среды: содержат [параметры конфигурации](xref:blazor/fundamentals/configuration) для приложения.

* `Program.cs`: точка входа в приложение, которая настраивает [узел](xref:fundamentals/host/generic-host) ASP.NET Core.

* `Startup.cs`: Содержит логику для запуска приложения. В классе `Startup` определены два метода:

  * `ConfigureServices`. Настраивает службы [внедрения зависимостей](xref:fundamentals/dependency-injection) для приложения. Службы добавляются путем вызова метода <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>. В контейнер службы добавляется служба `WeatherForecastService`, которая используется примером компонента `FetchData`.
  * `Configure`. Настраивает конвейер обработки запросов для приложения.
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> вызывается с целью настройки конечной точки для соединения в режиме реального времени с браузером. Соединение создается с помощью [SignalR](xref:signalr/introduction), платформы для добавления веб-функций реального времени в приложения.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) вызывается для настройки корневой страницы приложения (`Pages/_Host.cshtml`) и обеспечения навигации.
