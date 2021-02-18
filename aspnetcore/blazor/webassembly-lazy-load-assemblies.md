---
title: Сборки с отложенной загрузкой в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отложенную загрузку сборок в приложениях ASP.NET Core Blazor WebAssembly.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: e8589a1e288c39b487673fafc04c59fa07916335
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280661"
---
# <a name="lazy-load-assemblies-in-aspnet-core-blazor-webassembly"></a>Сборки с отложенной загрузкой в ASP.NET Core Blazor WebAssembly

Производительность запуска приложения Blazor WebAssembly можно повысить, отложив загрузку некоторых сборок приложений, пока они не потребуются — это называется *отложенной загрузкой*. Например, сборки, которые используются только для отрисовки лишь одного компонента, могут быть настроены на загрузку только в том случае, если пользователь переходит к этому компоненту. После загрузки сборки кэшируются на стороне клиента и доступны для всех последующих переходов.

Функция отложенной загрузки Blazor позволяет помечать сборки приложений для отложенной загрузки, чтобы загружать их во время выполнения, когда пользователь переходит по определенному маршруту. Эта функция содержит изменения в файле проекта и изменения в маршрутизаторе приложения.

> [!NOTE]
> Отложенная загрузка сборки ничего не даст приложениям Blazor Server, так как сборки не загружаются на клиент в приложении Blazor Server.

## <a name="project-file"></a>Файл проекта

Пометьте сборки для отложенной загрузки в файле проекта приложения (`.csproj`) с помощью элемента `BlazorWebAssemblyLazyLoad`. Используйте имя сборки с расширением `.dll`. Платформа Blazor предотвращает загрузку сборок, заданных этой группой элементов, при запуске приложения. Следующий пример помечает большую пользовательскую сборку (`GrantImaharaRobotControls.dll`) для отложенной загрузки. Если сборка, помеченная для отложенной загрузки, содержит зависимости, они также должны быть помечены для отложенной загрузки в файле проекта.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>`Router`

Компонент `Router` в Blazor определяет, какие сборки Blazor требуют компоненты, поддерживающие маршрутизацию. Компонент `Router` также отвечает за рендеринг компонента для маршрута, по которому переходит пользователь. Компонент `Router` поддерживает функцию `OnNavigateAsync`, которую можно использовать в сочетании с отложенной загрузкой.

В компоненте `Router` приложения (`App.razor`):

* Добавьте обратный вызов `OnNavigateAsync`. Обработчик `OnNavigateAsync` вызывается, когда пользователь выполняет следующие действия:
  * попадает на маршрут в первый раз, перейдя к нему непосредственно из браузера;
  * переходит на новый маршрут с помощью ссылки или вызова <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.
* Если сборки с отложенной загрузкой содержат маршрутизируемые компоненты, добавьте в компонент [Список](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (например, с именем `lazyLoadedAssemblies`). Сборки передаются обратно в коллекцию <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, если они содержат маршрутизируемые компоненты. Платформа выполняет поиск маршрутов в сборках и обновляет коллекцию маршрутов при обнаружении новых маршрутов.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Если обратный вызов `OnNavigateAsync` создает необработанное исключение, вызывается [пользовательский интерфейс ошибок Blazor](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).

### <a name="assembly-load-logic-in-onnavigateasync"></a>Логика загрузки сборок в `OnNavigateAsync`

`OnNavigateAsync` имеет параметр `NavigationContext`, который предоставляет сведения о текущем асинхронном событии навигации, включая целевой путь (`Path`) и токен отмены (`CancellationToken`):

* Свойство `Path` — это путь назначения пользователя относительно базового пути приложения, например `/robot`.
* `CancellationToken` можно использовать для отслеживания отмены асинхронной задачи. `OnNavigateAsync` автоматически отменяет текущую выполняемую задачу навигации, когда пользователь переходит на другую страницу.

В `OnNavigateAsync` реализуйте логику для определения загружаемых сборок. Доступны следующие варианты:

* Условные проверки в методе `OnNavigateAsync`.
* Таблица подстановки сопоставляет маршруты с именами сборок, которые либо вставляются в компонент, либо реализуются в блоке [`@code`](xref:mvc/views/razor#code).

`LazyAssemblyLoader` — предоставляемая платформой отдельная служба для загрузки сборок. Вставка `LazyAssemblyLoader` в компонент `Router`:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader` предоставляет метод `LoadAssembliesAsync`, который:

* Использует взаимодействие JS для выборки сборок через сетевой вызов.
* Загружает сборки в среду выполнения в WebAssembly в браузере.

Реализация отложенной загрузки на платформе поддерживает отложенную загрузку с предварительной отрисовкой в размещенном решении Blazor. Во время предварительной отрисовки предполагается, что будут загружены все сборки, включая те, которые отмечены для отложенной загрузки. Вручную зарегистрируйте `LazyAssemblyLoader` в методе `Startup.ConfigureServices` проекта *сервера* (`Startup.cs`):

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>Взаимодействие пользователей с содержимым `<Navigating>`

При загрузке сборок, что может занять несколько секунд, компонент `Router` может показать пользователю, что происходит переход на страницу:

* Добавьте директиву [`@using`](xref:mvc/views/razor#using) для пространства имен <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>.
* Добавьте тег `<Navigating>` в компонент с разметкой, отображаемой во время событий перехода страницы.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a>Обработка отмены в `OnNavigateAsync`

Объект `NavigationContext`, переданный в обратный вызов `OnNavigateAsync`, содержит `CancellationToken`, который задается при возникновении нового события навигации. Обратный вызов `OnNavigateAsync` должен вызываться, если этот токен отмены установлен так, чтобы не выполнять обратный вызов `OnNavigateAsync` для устаревшей навигации.

Если пользователь переходит на маршрут A, а затем сразу же переходит на маршрут B, приложение не должно продолжать выполнять ответный вызов `OnNavigateAsync` для маршрута A:

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> Если не отбросить маршрут, если токен отмены в `NavigationContext` отменяется, то это может привести к непредсказуемому поведению, например рендерингу компонента из предыдущей навигации.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>События `OnNavigateAsync` и переименованные файлы сборки

Загрузчик ресурсов использует имена сборок, определенные в файле `blazor.boot.json`. Если [сборки переименованы](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), то имена сборок, используемые в методах `OnNavigateAsync`, и имена сборок в файле `blazor.boot.json` не синхронизированы.

Чтобы исправить это, сделайте следующее.

* Проверьте, выполняется ли приложение в рабочей среде при определении используемых имен сборок.
* Сохраните имена переименованных сборок в отдельном файле и выполните чтение из этого файла, чтобы определить, какое имя сборки использовать в методах `LazyLoadAssemblyService` и `OnNavigateAsync`.

### <a name="complete-example"></a>Полный пример

Следующий полный компонент `Router` демонстрирует загрузку сборки `GrantImaharaRobotControls.dll`, когда пользователь переходит к `/robot`. Во время перехода страниц для пользователя отображается стилизованное сообщение.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a>Диагностика

* Если происходит непредвиденная отрисовка (например, при отрисовке компонента из предыдущей навигации), убедитесь, что код создает исключение, если задан токен отмены.
* Если сборки по-прежнему загружаются при запуске приложения, убедитесь, что в файле проекта сборка помечена для отложенной загрузки.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/webassembly-performance-best-practices>
