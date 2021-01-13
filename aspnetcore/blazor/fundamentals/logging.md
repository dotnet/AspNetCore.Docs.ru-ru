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
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="c36d0-103">Ведение журналов ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c36d0-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="c36d0-104">Настройте пользовательское ведение журнала в приложениях Blazor WebAssembly с помощью свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c36d0-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="c36d0-105">Добавьте пространство имен для <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> в `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="c36d0-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="c36d0-106">В методе `Program.Main` в файле `Program.cs` задайте минимальный уровень ведения журнала с помощью <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> и добавьте пользовательский поставщик ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="c36d0-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="c36d0-107">Свойство `Logging` имеет тип <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, поэтому все методы расширения, доступные в <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, также доступны в `Logging`.</span><span class="sxs-lookup"><span data-stu-id="c36d0-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="c36d0-108">Конфигурацию ведения журнала можно загрузить из файлов параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="c36d0-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="c36d0-109">Для получения дополнительной информации см. <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="c36d0-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="c36d0-110">Ведение журнала клиента SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="c36d0-110">SignalR .NET client logging</span></span>

<span data-ttu-id="c36d0-111">Вставьте <xref:Microsoft.Extensions.Logging.ILoggerProvider>, чтобы добавить `WebAssemblyConsoleLogger` к регистраторам, передаваемым в <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="c36d0-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="c36d0-112">В отличие от традиционных <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` является оболочкой для API-интерфейсов ведения журнала, характерных для браузера (например, `console.log`).</span><span class="sxs-lookup"><span data-stu-id="c36d0-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="c36d0-113">Использование `WebAssemblyConsoleLogger` делает возможным ведение журнала в Mono внутри контекста браузера.</span><span class="sxs-lookup"><span data-stu-id="c36d0-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="c36d0-114">`WebAssemblyConsoleLogger` является [внутренними](/dotnet/csharp/language-reference/keywords/internal) компонентом, который недоступен для непосредственного использования в коде разработчика.</span><span class="sxs-lookup"><span data-stu-id="c36d0-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="c36d0-115">Добавьте пространство имен для <xref:Microsoft.Extensions.Logging?displayProperty=fullName> и внедрите <xref:Microsoft.Extensions.Logging.ILoggerProvider> в компонент.</span><span class="sxs-lookup"><span data-stu-id="c36d0-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="c36d0-116">В [методе `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) компонента используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c36d0-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="c36d0-117">Общие рекомендации по ведению журнала ASP.NET Core, относящиеся к Blazor Server, см. в разделе <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="c36d0-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="c36d0-118">Ведение журнала в компонентах Razor</span><span class="sxs-lookup"><span data-stu-id="c36d0-118">Log in Razor components</span></span>

<span data-ttu-id="c36d0-119">Средства ведения журнала учитывают конфигурацию запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="c36d0-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="c36d0-120">Директива `using` для <xref:Microsoft.Extensions.Logging> обеспечивает поддержку завершения [IntelliSense](/visualstudio/ide/using-intellisense) для таких интерфейсов API, как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="c36d0-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="c36d0-121">Приведенный ниже пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILogger> в компонентах.</span><span class="sxs-lookup"><span data-stu-id="c36d0-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="c36d0-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c36d0-122">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="c36d0-123">Приведенный ниже пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILoggerFactory> в компонентах.</span><span class="sxs-lookup"><span data-stu-id="c36d0-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="c36d0-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="c36d0-124">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="c36d0-125">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c36d0-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
