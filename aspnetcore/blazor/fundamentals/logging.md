---
title: Ведение журналов ASP.NET Core Blazor
author: guardrex
description: Сведения о ведении журналов в Blazor приложениях, включая настройку уровня ведения журнала и запись сообщений журнала из компонентов Razor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97666837"
---
# <a name="aspnet-core-no-locblazor-logging"></a>Ведение журналов ASP.NET Core Blazor

::: zone pivot="webassembly"

Настройте пользовательское ведение журнала в приложениях Blazor WebAssembly с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType>.

Добавьте пространство имен для <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> в `Program.cs`.

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

В методе `Program.Main` в файле `Program.cs` задайте минимальный уровень ведения журнала с помощью <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> и добавьте пользовательский поставщик ведения журнала.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

Свойство `Logging` имеет тип <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, поэтому все методы расширения, доступные в <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, также доступны в `Logging`.

Конфигурацию ведения журнала можно загрузить из файлов параметров приложения. Для получения дополнительной информации см. <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="no-locsignalr-net-client-logging"></a>Ведение журнала клиента SignalR .NET

Вставьте <xref:Microsoft.Extensions.Logging.ILoggerProvider>, чтобы добавить `WebAssemblyConsoleLogger` к регистраторам, передаваемым в <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. В отличие от традиционных <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` является оболочкой для API-интерфейсов ведения журнала, характерных для браузера (например, `console.log`). Использование `WebAssemblyConsoleLogger` делает возможным ведение журнала в Mono внутри контекста браузера.

> [!NOTE]
> `WebAssemblyConsoleLogger` является [внутренними](/dotnet/csharp/language-reference/keywords/internal) компонентом, который недоступен для непосредственного использования в коде разработчика.

Добавьте пространство имен для <xref:Microsoft.Extensions.Logging?displayProperty=fullName> и внедрите <xref:Microsoft.Extensions.Logging.ILoggerProvider> в компонент.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

В [методе `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) компонента используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>.

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Общие рекомендации по ведению журнала ASP.NET Core, относящиеся к Blazor Server, см. в разделе <xref:fundamentals/logging/index>.

::: zone-end

## <a name="log-in-no-locrazor-components"></a>Ведение журнала в компонентах Razor

Средства ведения журнала учитывают конфигурацию запуска приложения.

Директива `using` для <xref:Microsoft.Extensions.Logging> обеспечивает поддержку завершения [IntelliSense](/visualstudio/ide/using-intellisense) для таких интерфейсов API, как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.

Приведенный ниже пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILogger> в компонентах.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

Приведенный ниже пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILoggerFactory> в компонентах.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/logging/index>
