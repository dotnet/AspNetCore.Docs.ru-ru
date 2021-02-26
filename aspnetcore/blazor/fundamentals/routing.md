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
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279888"
---
# <a name="aspnet-core-blazor-routing"></a>Маршрутизация ASP.NET Core Blazor

В этой статье описывается, как управлять маршрутизацией запросов и как использовать компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> для создания навигационных ссылок в приложениях Blazor.

## <a name="route-templates"></a>Шаблоны маршрутов

Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет выполнять маршрутизацию в компоненты Razor в приложении Blazor. Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> используется в компоненте `App` приложений Blazor.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

При компиляции компонента Razor (`.razor`) с [директивой `@page`](xref:mvc/views/razor#page) созданный класс компонента предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.

При запуске приложения выполняется проверка сборки, указанной как `AppAssembly` маршрутизатора, для сбора сведений о маршрутах для компонентов приложения с <xref:Microsoft.AspNetCore.Components.RouteAttribute>.

В среде выполнения компонент <xref:Microsoft.AspNetCore.Components.RouteView> выполняет следующие операции:

* получает <xref:Microsoft.AspNetCore.Components.RouteData> от <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе со всеми параметрами маршрута;
* преобразовывает указанный компонент для просмотра с его [макетом](xref:blazor/layouts), включая все вложенные макеты.

При необходимости укажите параметр <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> с классом макета для компонентов, которые не задают макет, с помощью [директивы `@layout`](xref:blazor/layouts#specify-a-layout-in-a-component). В шаблонах проекта Blazor платформы в качестве макета приложения по умолчанию указывается компонент `MainLayout` (`Shared/MainLayout.razor`). Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.

Компоненты поддерживают несколько шаблонов маршрутов с помощью нескольких [директив `@page`](xref:mvc/views/razor#page). Приведенный ниже пример компонента загружается при запросах к `/BlazorRoute` и `/DifferentBlazorRoute`.

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> Для правильного разрешения URL-адресов приложение должно содержать тег `<base>` в файле `wwwroot/index.html` (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href`. Дополнительные сведения см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Предоставление пользовательского содержимого, когда содержимое не найдено

Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.

В компоненте `App` задайте пользовательское содержимое в шаблоне <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

Теги `<NotFound>` могут содержать произвольные элементы, например другие интерактивные компоненты. Сведения о применении макета по умолчанию к содержимому <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> см. в разделе <xref:blazor/layouts#default-layout>.

## <a name="route-to-components-from-multiple-assemblies"></a>Маршрутизация к компонентам из нескольких сборок

Используйте параметр <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, чтобы указать дополнительные сборки для компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>, которые следует учитывать при поиске маршрутизируемых компонентов. В дополнение к сборке, указанной в `AppAssembly`, проверяются дополнительные сборки. В приведенном ниже примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой [библиотеке классов компонентов](xref:blazor/components/class-libraries). В приведенном ниже примере <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> приводится поддержка маршрутизации для `Component1`.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a>Параметры маршрута

Маршрутизатор использует параметры маршрута для заполнения соответствующих [параметров компонента](xref:blazor/components/index#component-parameters) с тем же именем. В именах параметров маршрута регистр не учитывается. В приведенном ниже примере параметр `text` присваивает значение сегмента маршрута свойству `Text` компонента. Если запрос выполняется для `/RouteParameter/amazing`, содержимое тега `<h1>` отображается как `Blazor is amazing!`.

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Поддерживаются необязательные параметры. В следующем примере необязательный параметр `text` назначает значение сегмента маршрута свойству `Text` компонента. Если сегмента нет, для `Text` устанавливается значение `fantastic`.

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Необязательные параметры не поддерживаются. В приведенном ниже примере применяются две [директивы `@page`](xref:mvc/views/razor#page). Первая позволяет переходить к компоненту без параметра. Вторая директива присваивает значение параметра маршрута `{text}` свойству `Text` компонента.

`Pages/RouteParameter.razor`:

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

Чтобы разрешить переход к тому же компоненту с другим значением необязательного параметра, используйте [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) вместо [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods). Принимая во внимание предыдущий пример, используйте `OnParametersSet`, когда пользователь должен иметь возможность переходить от `/RouteParameter` к `/RouteParameter/amazing` или от `/RouteParameter/amazing` к `/RouteParameter`:

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Ограничения маршрута

Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.

В следующем примере маршрут к компоненту `User` соответствует только в следующих случаях:

* в URL-адресе запроса имеется сегмент маршрута `Id`;
* сегмент `Id` имеет целочисленный тип (`int`).

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

Доступны ограничения маршрутов, приведенные в следующей таблице. Сведения об ограничениях маршрута, соответствующих инвариантным языку и региональным параметрам, см. в предупреждении внизу таблицы.

| Ограничение | Пример           | Примеры совпадений                                                                  | Инвариант<br>язык и региональные параметры<br>соответствие |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Нет                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Да                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Да                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Да                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Да                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Нет                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Да                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Да                              |

> [!WARNING]
> Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или <xref:System.DateTime>), всегда используют инвариантные язык и региональные параметры. Эти ограничения предполагают, что URL-адрес является нелокализуемым.

## <a name="routing-with-urls-that-contain-dots"></a>Маршрутизация URL-адресов, содержащих точки

Для размещенных приложений Blazor WebAssembly и Blazor Server шаблон маршрута по умолчанию на стороне сервера предполагает, что если последний сегмент URL-адреса запроса содержит точку (`.`), то запрашивается файл. Например, URL-адрес `https://localhost.com:5001/example/some.thing` интерпретируется маршрутизатором как запрос файла с именем `some.thing`. Без дополнительной конфигурации приложение возвращает ответ *404 — Not Found* (не найдено), если предполагалась маршрутизация `some.thing` к компоненту с [директивой `@page`](xref:mvc/views/razor#page), а `some.thing` — это значение параметра маршрута. Чтобы использовать маршрут с одним параметром или несколькими, содержащими точку, в приложении необходимо настроить маршрут с помощью пользовательского шаблона.

Рассмотрим приведенный ниже компонент `Example`, который может получать параметр маршрута из последнего сегмента URL-адреса.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

Чтобы позволить приложению *`Server`* размещенного решения Blazor WebAssembly маршрутизировать запрос с точкой в параметре маршрута `param`, добавьте шаблон резервного маршрута к файлу с дополнительным параметром в `Startup.Configure`.

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Чтобы позволить приложению Blazor Server маршрутизировать запрос с точкой в параметре маршрута `param`, добавьте шаблон резервного маршрута к странице с дополнительным параметром в `Startup.Configure`.

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Для получения дополнительной информации см. <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Параметры маршрута catch-all

::: moniker range=">= aspnetcore-5.0"

В компонентах поддерживаются параметры маршрута catch-all, которые захватывают пути в нескольких папках.

Параметры маршрута catch-all

* Его имя должно соответствовать имени сегмента маршрута. Регистр в имени не учитывается.
* Тип `string`. Платформа не обеспечивает автоматическое приведение.
* В конце URL-адреса.

`Pages/CatchAll.razor`:

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

Для URL-адреса `/catch-all/this/is/a/test` с шаблоном маршрута `/catch-all/{*pageRoute}` значение `PageRoute` равно `this/is/a/test`.

Косые черты и сегменты захваченного пути декодированы. Для шаблона маршрута `/catch-all/{*pageRoute}` URL-адрес `/catch-all/this/is/a%2Ftest%2A` возвращает `this/is/a/test*`.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Параметры маршрута catch-all поддерживаются в ASP.NET Core 5.0 и более поздних версий. Для получения дополнительных сведений выберите вариант этой статьи для версии 5.0.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>URI и вспомогательные инструменты состояния навигации

Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для управления кодами URI и навигацией в коде C#. <xref:Microsoft.AspNetCore.Components.NavigationManager> предоставляет события и методы, приведенные в следующей таблице.

| Член | Описание |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Возвращает текущий абсолютный URI. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Получает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI. Как правило, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> соответствует атрибуту `href` элемента документа `<base>` в `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Переходит по указанному URI. Если значение `forceLoad` равно `true`:<ul><li>маршрутизация на стороне клиента обходится;</li><li>браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Событие, возникающее при изменении расположения навигации. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Преобразует относительный URI в абсолютный. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Если указан базовый URI (например, URI, возвращенный ранее <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), преобразует абсолютный URI в URI относительно базового префикса. |

Для события <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> предоставляет следующие сведения о событиях навигации.

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>. URL-адрес нового расположения.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>. Если `true`, Blazor перехватывает навигацию из браузера. Если `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> приводит к переходу.

Приведенный ниже компонент выполняет следующие действия:

* переходит к компоненту `Counter` приложения (`Pages/Counter.razor`) при нажатии кнопки с помощью <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>;
* обрабатывает событие изменения расположения путем оформления подписки на <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.
  * При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется. После отсоединения метода становится возможной сборка мусора для компонента.
  * При нажатии кнопки реализация средства ведения журнала записывает следующие сведения.

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

## <a name="query-string-and-parse-parameters"></a>Строка запроса и параметры анализа

Строка запроса получается из свойства <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType>.

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

Чтобы проанализировать параметры строки запроса:

* Приложение может использовать API-интерфейс <xref:Microsoft.AspNetCore.WebUtilities>. Если этот API-интерфейс недоступен приложению, добавьте ссылку на пакет [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) в файл проекта приложения.
* Получите значение после анализа строки запроса с помощью <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.

Приведенный ниже пример компонента `ParseQueryString` анализирует ключ параметра строки запроса с именем `ship`. Например, для пары "ключ — значение" `?ship=Tardis` в строке запроса URL-адреса записывается значение `Tardis` в `queryValue`. В приведенном ниже примере происходит переход к приложению по URL-адресу `https://localhost:5001/parse-query-string?ship=Tardis`.

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a>Новые компоненты `NavLink` и `NavMenu`

Используйте при создании ссылок навигации компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> вместо HTML-элементов гиперссылок (`<a>`). Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу. Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации. При необходимости назначьте имя класса CSS свойству <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>, чтобы применить пользовательский класс CSS к отображаемой ссылке, если текущий маршрут совпадает с `href`.

Следующий компонент `NavMenu` создает панель навигации [`Bootstrap`](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> Компонент `NavMenu` (`NavMenu.razor`) находится в папке `Shared` приложения, созданного на основе шаблонов проектов Blazor.

Существует два параметра <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>, которые можно назначить атрибуту `Match` элемента `<NavLink>`:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует всему текущему URL-адресу;
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*по умолчанию*). <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует любому префиксу текущего URL-адреса.

В предыдущем примере <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`). Второй <xref:Microsoft.AspNetCore.Components.Routing.NavLink> получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `component` (например, `https://localhost:5001/component` и `https://localhost:5001/component/another-segment`).

Дополнительные атрибуты компонента <xref:Microsoft.AspNetCore.Components.Routing.NavLink> передаются в отображаемый тег привязки. В следующем примере компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> включает атрибут `target`.

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

Отобразится следующая разметка HTML.

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для рендеринга компонентов `NavLink` в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента `NavLink` используется переменная цикла приращения:
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
> Использование переменной индекса в этом сценарии обязательно для **любого** дочернего компонента, который использует переменную цикла в своем [дочернем содержимом](xref:blazor/components/index#child-content), а не только для компонента `NavLink`.
>
> Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:
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

## <a name="aspnet-core-endpoint-routing-integration"></a>Интеграция маршрутизации конечных точек ASP.NET Core

*Этот раздел относится только к приложениям Blazor Server.*

Blazor Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing). Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в `Startup.Configure`.

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

Типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server. По соглашению страница *узла* обычно называется `_Host.cshtml` и находится в папке `Pages` приложения.

Маршрут, указанный в файле узла, называется *резервным маршрутом*, так как он работает с низким приоритетом в соответствии с правилами маршрутизации. Резервный маршрут используется, если другие маршруты не сопоставляются. Это позволяет приложению использовать другие контроллеры и страницы, не мешая маршрутизации компонента в приложении Blazor Server.

Сведения о настройке <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> для размещения сервера по некорневому URL-адресу см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.
