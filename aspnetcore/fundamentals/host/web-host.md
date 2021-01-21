---
title: Веб-узел ASP.NET Core
author: rick-anderson
description: Сведения о веб-узле в ASP.NET Core, который отвечает за запуск приложений и управление временем существования.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 98be96bf60441cf09a315dbd1c60e109a7a08afe
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253115"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="3f9d1-103">Веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f9d1-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="3f9d1-104">Приложения ASP.NET Core настраивают и запускают *узел*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="3f9d1-105">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="3f9d1-106">Узел настраивает как минимум сервер и конвейер обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="3f9d1-107">Узел также может настроить ведение журнала, внедрение зависимостей и конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f9d1-108">В этой статье описывается веб-узел, доступ к которому предоставляется только для обеспечения обратной совместимости.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="3f9d1-109">Для приложений всех типов рекомендуется использовать [универсальный узел](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f9d1-110">В этой статье описывается веб-узел, который предназначен для размещения веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="3f9d1-111">Для приложений других типов используйте [универсальный узел](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="3f9d1-112">Создание узла</span><span class="sxs-lookup"><span data-stu-id="3f9d1-112">Set up a host</span></span>

<span data-ttu-id="3f9d1-113">Создайте узел с помощью экземпляра [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="3f9d1-114">Обычно это делается в точке входа в приложение, то есть в методе `Main`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="3f9d1-115">В шаблонах проектов метод `Main` находится в файле *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="3f9d1-116">Обычно приложение вызывает [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), чтобы начать настройку узла:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="3f9d1-117">Код, который вызывает `CreateDefaultBuilder`, находится в методе `CreateWebHostBuilder`, что отделяет его от кода в методе `Main`, который вызывает `Run` для объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="3f9d1-118">Такое отделение требуется, если вы используете [инструменты Entity Framework Core](/ef/core/miscellaneous/cli/).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="3f9d1-119">Эти инструменты используют метод `CreateWebHostBuilder`, который они могут вызвать во время разработки для настройки узла без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="3f9d1-120">В качестве альтернативного способа можно использовать реализацию `IDesignTimeDbContextFactory`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="3f9d1-121">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="3f9d1-122">Метод `CreateDefaultBuilder` выполняет указанные ниже задачи.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="3f9d1-123">Настраивает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="3f9d1-124">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel/options>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
* <span data-ttu-id="3f9d1-125">Настраивает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-125">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="3f9d1-126">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-126">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
::: moniker-end
* <span data-ttu-id="3f9d1-127">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-127">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="3f9d1-128">Загружает [конфигурацию узла](#host-configuration-values) из:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-128">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="3f9d1-129">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="3f9d1-130">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-130">Command-line arguments.</span></span>
* <span data-ttu-id="3f9d1-131">Загружает конфигурацию приложения в следующем порядке из:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-131">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="3f9d1-132">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-132">*appsettings.json*.</span></span>
  * <span data-ttu-id="3f9d1-133">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="3f9d1-133">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="3f9d1-134">[секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок;</span><span class="sxs-lookup"><span data-stu-id="3f9d1-134">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="3f9d1-135">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-135">Environment variables.</span></span>
  * <span data-ttu-id="3f9d1-136">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-136">Command-line arguments.</span></span>
* <span data-ttu-id="3f9d1-137">Настраивает [ведение журнала](xref:fundamentals/logging/index) для выходных данных консоли и отладки.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-137">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="3f9d1-138">Ведение журнала включает в себя правила [фильтрации журналов](xref:fundamentals/logging/index#log-filtering), заданные в разделе конфигурации ведения журнала в файле *appsettings.json* или *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-138">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="3f9d1-139">При работе за службами IIS с [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module)`CreateDefaultBuilder` обеспечивает [интеграцию со службами IIS](xref:host-and-deploy/iis/index) для настройки базового адреса и порта приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-139">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="3f9d1-140">Кроме того, интеграция со службами IIS также позволяет настраивать [перехват приложением ошибок запуска](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-140">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="3f9d1-141">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-141">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="3f9d1-142">Устанавливает для [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) значение `true`, если приложение находится в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-142">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="3f9d1-143">Дополнительные сведения см. в разделе [Проверка области](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-143">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="3f9d1-144">Настройки, определенные `CreateDefaultBuilder`, можно переопределить и усилить с помощью [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) и других методов и методов расширения [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-144">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="3f9d1-145">Ниже приведены некоторые примеры:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-145">A few examples follow:</span></span>

* <span data-ttu-id="3f9d1-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) используется для указания дополнительного объекта `IConfiguration` для приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-146">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="3f9d1-147">Следующий вызов `ConfigureAppConfiguration` добавляет делегат, чтобы включить конфигурацию приложения в файл *appsettings.xml*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-147">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="3f9d1-148">`ConfigureAppConfiguration` можно вызывать несколько раз.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-148">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="3f9d1-149">Обратите внимание, что эта конфигурация не распространяется на узел (например, URL-адреса серверов или среду).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-149">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="3f9d1-150">См. раздел [Значения конфигурации узла](#host-configuration-values).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-150">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="3f9d1-151">Следующий вызов `ConfigureLogging` добавляет делегата, чтобы настроить минимальный уровень ведения журнала ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) для [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-151">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="3f9d1-152">Этот параметр переопределяет параметры в *appsettings.Development.json* (`LogLevel.Debug`) и *appsettings.Production.json* (`LogLevel.Error`), заданные `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-152">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="3f9d1-153">`ConfigureLogging` можно вызывать несколько раз.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-153">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="3f9d1-154">При следующем вызове к `ConfigureKestrel` переопределяется значение по умолчанию [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), равное 30 000 000 байтов, установленное при настройке Kestrel методом `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-154">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="3f9d1-155">Следующий вызов [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) переопределяет значение по умолчанию [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize), равное 30 000 000 байтов, установленное при настройке Kestrel методом `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-155">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="3f9d1-156">[Корень содержимого](xref:fundamentals/index#content-root) определяет, где узел ищет файлы содержимого, например файлы представлений MVC.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-156">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="3f9d1-157">При запуске приложения из корневой папки проекта эта папка используется в качестве корня содержимого.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-157">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="3f9d1-158">Такое поведение по умолчанию принято в [Visual Studio](https://visualstudio.microsoft.com) и [шаблонах dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-158">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="3f9d1-159">Дополнительные сведения о конфигурации приложения см. в разделе <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-159">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="3f9d1-160">Помимо использования статического метода `CreateDefaultBuilder`, в ASP.NET Core 2.x поддерживается создание узла на основе [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-160">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="3f9d1-161">При настройке узла можно предоставить методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) и [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-161">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="3f9d1-162">Если используется класс `Startup`, в нем должен быть определен метод `Configure`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-162">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="3f9d1-163">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-163">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="3f9d1-164">Несколько вызовов `ConfigureServices` добавляются друг к другу.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-164">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="3f9d1-165">При нескольких вызовах `Configure` или `UseStartup` в `WebHostBuilder` предыдущие параметры заменяются.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-165">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="3f9d1-166">Значения конфигурации узла</span><span class="sxs-lookup"><span data-stu-id="3f9d1-166">Host configuration values</span></span>

<span data-ttu-id="3f9d1-167">Для задания значений конфигурации узла класс [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) поддерживает следующие подходы:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-167">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="3f9d1-168">конфигурация построителя узла, которая включает в себя переменные среды в формате `ASPNETCORE_{configurationKey}`,</span><span class="sxs-lookup"><span data-stu-id="3f9d1-168">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="3f9d1-169">Например, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-169">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="3f9d1-170">Расширения, такие как [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) и [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (см. раздел [Переопределение конфигурации](#override-configuration)).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-170">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="3f9d1-171">метод [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) и связанный ключ.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-171">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="3f9d1-172">Значение, задаваемое с помощью `UseSetting`, всегда является строкой независимо от типа.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-172">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="3f9d1-173">Хост использует значение, заданное последним.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-173">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="3f9d1-174">Дополнительные сведения см. в подразделе [Переопределение конфигурации](#override-configuration) следующего раздела.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-174">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="3f9d1-175">Ключ приложения (имя)</span><span class="sxs-lookup"><span data-stu-id="3f9d1-175">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f9d1-176">Свойство `IWebHostEnvironment.ApplicationName` задается автоматически при вызове [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) или [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-176">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="3f9d1-177">Значение присваивается имени сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-177">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="3f9d1-178">Чтобы явно задать значение, используйте [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-178">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3f9d1-179">Свойство [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) задается автоматически при вызове [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) или [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-179">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="3f9d1-180">Значение присваивается имени сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-180">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="3f9d1-181">Чтобы явно задать значение, используйте [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-181">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="3f9d1-182">**Ключ**: applicationName</span><span class="sxs-lookup"><span data-stu-id="3f9d1-182">**Key**: applicationName</span></span>  
<span data-ttu-id="3f9d1-183">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-183">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-184">**По умолчанию**: имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-184">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="3f9d1-185">**Задается с помощью**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-185">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="3f9d1-186">**Переменная среды**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-186">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="3f9d1-187">Перехват ошибок при загрузке</span><span class="sxs-lookup"><span data-stu-id="3f9d1-187">Capture Startup Errors</span></span>

<span data-ttu-id="3f9d1-188">Этот параметр управляет перехватом ошибок при загрузке.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-188">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="3f9d1-189">**Ключ**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="3f9d1-189">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="3f9d1-190">**Тип**: *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="3f9d1-190">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="3f9d1-191">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-191">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="3f9d1-192">**Задается с помощью**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-192">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="3f9d1-193">**Переменная среды**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-193">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="3f9d1-194">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-194">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="3f9d1-195">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-195">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="3f9d1-196">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="3f9d1-196">Content root</span></span>

<span data-ttu-id="3f9d1-197">Этот параметр определяет то, где ASP.NET Core начинает искать файлы содержимого.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-197">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="3f9d1-198">**Ключ**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="3f9d1-198">**Key**: contentRoot</span></span>  
<span data-ttu-id="3f9d1-199">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-199">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-200">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-200">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="3f9d1-201">**Задается с помощью**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-201">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="3f9d1-202">**Переменная среды**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-202">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="3f9d1-203">Корневой каталог содержимого также используется в качестве базового пути для [корневого каталога документов](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-203">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="3f9d1-204">Если путь к корневому каталогу содержимого не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-204">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="3f9d1-205">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="3f9d1-205">For more information, see:</span></span>

* [<span data-ttu-id="3f9d1-206">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="3f9d1-206">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="3f9d1-207">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="3f9d1-207">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="3f9d1-208">Подробные сообщения об ошибках</span><span class="sxs-lookup"><span data-stu-id="3f9d1-208">Detailed Errors</span></span>

<span data-ttu-id="3f9d1-209">Определяет, следует ли перехватывать подробные сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-209">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="3f9d1-210">**Ключ**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="3f9d1-210">**Key**: detailedErrors</span></span>  
<span data-ttu-id="3f9d1-211">**Тип**: *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="3f9d1-211">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="3f9d1-212">**Значение по умолчанию**: false</span><span class="sxs-lookup"><span data-stu-id="3f9d1-212">**Default**: false</span></span>  
<span data-ttu-id="3f9d1-213">**Задается с помощью**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-213">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="3f9d1-214">**Переменная среды**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-214">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="3f9d1-215">Если этот параметр включен (или если параметр <a href="#environment">Среда</a> имеет значение `Development`), приложение перехватывает подробные исключения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-215">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="3f9d1-216">Среда</span><span class="sxs-lookup"><span data-stu-id="3f9d1-216">Environment</span></span>

<span data-ttu-id="3f9d1-217">Задает среду приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-217">Sets the app's environment.</span></span>

<span data-ttu-id="3f9d1-218">**Ключ**: environment</span><span class="sxs-lookup"><span data-stu-id="3f9d1-218">**Key**: environment</span></span>  
<span data-ttu-id="3f9d1-219">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-219">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-220">**По умолчанию**: Рабочие</span><span class="sxs-lookup"><span data-stu-id="3f9d1-220">**Default**: Production</span></span>  
<span data-ttu-id="3f9d1-221">**Задается с помощью**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-221">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="3f9d1-222">**Переменная среды**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-222">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="3f9d1-223">В качестве среды можно указать любое значение.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-223">The environment can be set to any value.</span></span> <span data-ttu-id="3f9d1-224">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-224">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="3f9d1-225">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-225">Values aren't case sensitive.</span></span> <span data-ttu-id="3f9d1-226">По умолчанию значение параметра *Среда* считывается из переменной среды `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-226">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="3f9d1-227">При использовании [Visual Studio](https://visualstudio.microsoft.com) переменные среды можно задавать в файле *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-227">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="3f9d1-228">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-228">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="3f9d1-229">Начальные сборки размещения</span><span class="sxs-lookup"><span data-stu-id="3f9d1-229">Hosting Startup Assemblies</span></span>

<span data-ttu-id="3f9d1-230">Задает начальные сборки размещения для приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-230">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="3f9d1-231">**Ключ**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="3f9d1-231">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="3f9d1-232">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-232">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-233">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="3f9d1-233">**Default**: Empty string</span></span>  
<span data-ttu-id="3f9d1-234">**Задается с помощью**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-234">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="3f9d1-235">**Переменная среды**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-235">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="3f9d1-236">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-236">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="3f9d1-237">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-237">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="3f9d1-238">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-238">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="3f9d1-239">HTTPS-порт</span><span class="sxs-lookup"><span data-stu-id="3f9d1-239">HTTPS Port</span></span>

<span data-ttu-id="3f9d1-240">Задайте порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-240">Set the HTTPS redirect port.</span></span> <span data-ttu-id="3f9d1-241">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-241">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3f9d1-242">**Ключ**: https_port **Тип**: *string*
**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-242">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="3f9d1-243">**Задается с помощью**: `UseSetting`
**переменной среды**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-243">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="3f9d1-244">Исключаемые начальные сборки размещения</span><span class="sxs-lookup"><span data-stu-id="3f9d1-244">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="3f9d1-245">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-245">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="3f9d1-246">**Ключ**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="3f9d1-246">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="3f9d1-247">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-247">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-248">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="3f9d1-248">**Default**: Empty string</span></span>  
<span data-ttu-id="3f9d1-249">**Задается с помощью**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-249">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="3f9d1-250">**Переменная среды**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-250">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="3f9d1-251">Предпочитать URL-адреса размещения</span><span class="sxs-lookup"><span data-stu-id="3f9d1-251">Prefer Hosting URLs</span></span>

<span data-ttu-id="3f9d1-252">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `WebHostBuilder`, вместо настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-252">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="3f9d1-253">**Ключ**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="3f9d1-253">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="3f9d1-254">**Тип**: *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="3f9d1-254">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="3f9d1-255">**Значение по умолчанию**: true</span><span class="sxs-lookup"><span data-stu-id="3f9d1-255">**Default**: true</span></span>  
<span data-ttu-id="3f9d1-256">**Задается с помощью**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-256">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="3f9d1-257">**Переменная среды**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-257">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="3f9d1-258">Запретить запуск размещения</span><span class="sxs-lookup"><span data-stu-id="3f9d1-258">Prevent Hosting Startup</span></span>

<span data-ttu-id="3f9d1-259">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-259">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="3f9d1-260">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-260">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="3f9d1-261">**Ключ**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="3f9d1-261">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="3f9d1-262">**Тип**: *bool* (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="3f9d1-262">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="3f9d1-263">**Значение по умолчанию**: false</span><span class="sxs-lookup"><span data-stu-id="3f9d1-263">**Default**: false</span></span>  
<span data-ttu-id="3f9d1-264">**Задается с помощью**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-264">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="3f9d1-265">**Переменная среды**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-265">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="3f9d1-266">URL-адреса сервера</span><span class="sxs-lookup"><span data-stu-id="3f9d1-266">Server URLs</span></span>

<span data-ttu-id="3f9d1-267">Задает IP-адреса или адреса узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-267">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="3f9d1-268">**Ключ**: urls</span><span class="sxs-lookup"><span data-stu-id="3f9d1-268">**Key**: urls</span></span>  
<span data-ttu-id="3f9d1-269">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-269">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-270">**По умолчанию**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="3f9d1-270">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="3f9d1-271">**Задается с помощью**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-271">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="3f9d1-272">**Переменная среды**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-272">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="3f9d1-273">Укажите разделенный точками с запятой (;) список префиксов URL-адресов, на которые сервер должен отвечать.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-273">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="3f9d1-274">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-274">For example, `http://localhost:123`.</span></span> <span data-ttu-id="3f9d1-275">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-275">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="3f9d1-276">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-276">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="3f9d1-277">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-277">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="3f9d1-278">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-278">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3f9d1-279">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-279">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="3f9d1-280">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-280">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="3f9d1-281">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-281">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>
::: moniker-end

### <a name="shutdown-timeout"></a><span data-ttu-id="3f9d1-282">Время ожидания завершения работы</span><span class="sxs-lookup"><span data-stu-id="3f9d1-282">Shutdown Timeout</span></span>

<span data-ttu-id="3f9d1-283">Определяет, как долго необходимо ожидать завершения работы веб-узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-283">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="3f9d1-284">**Ключ**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="3f9d1-284">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="3f9d1-285">**Тип**: *int*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-285">**Type**: *int*</span></span>  
<span data-ttu-id="3f9d1-286">**По умолчанию**: 5</span><span class="sxs-lookup"><span data-stu-id="3f9d1-286">**Default**: 5</span></span>  
<span data-ttu-id="3f9d1-287">**Задается с помощью**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-287">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="3f9d1-288">**Переменная среды**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-288">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="3f9d1-289">Хотя ключ принимает значение *int* с `UseSetting` (например, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), метод расширения [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) принимает [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-289">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="3f9d1-290">Во время ожидания размещение:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-290">During the timeout period, hosting:</span></span>

* <span data-ttu-id="3f9d1-291">Активирует [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-291">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="3f9d1-292">Пытается остановить размещенные службы, записывая в журнал все ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-292">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="3f9d1-293">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-293">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="3f9d1-294">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-294">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="3f9d1-295">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-295">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="3f9d1-296">Стартовая сборка</span><span class="sxs-lookup"><span data-stu-id="3f9d1-296">Startup Assembly</span></span>

<span data-ttu-id="3f9d1-297">Определяет сборку, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-297">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="3f9d1-298">**Ключ**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="3f9d1-298">**Key**: startupAssembly</span></span>  
<span data-ttu-id="3f9d1-299">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-299">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-300">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="3f9d1-300">**Default**: The app's assembly</span></span>  
<span data-ttu-id="3f9d1-301">**Задается с помощью**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-301">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="3f9d1-302">**Переменная среды**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-302">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="3f9d1-303">На сборку можно ссылаться по имени (`string`) или типу (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-303">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="3f9d1-304">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-304">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="3f9d1-305">Корневой веб-узел</span><span class="sxs-lookup"><span data-stu-id="3f9d1-305">Web root</span></span>

<span data-ttu-id="3f9d1-306">Задает относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-306">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="3f9d1-307">**Ключ**: webroot</span><span class="sxs-lookup"><span data-stu-id="3f9d1-307">**Key**: webroot</span></span>  
<span data-ttu-id="3f9d1-308">**Тип**: *string*</span><span class="sxs-lookup"><span data-stu-id="3f9d1-308">**Type**: *string*</span></span>  
<span data-ttu-id="3f9d1-309">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-309">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="3f9d1-310">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-310">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="3f9d1-311">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-311">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="3f9d1-312">**Задается с помощью**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-312">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="3f9d1-313">**Переменная среды**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="3f9d1-313">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="3f9d1-314">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="3f9d1-314">For more information, see:</span></span>

* [<span data-ttu-id="3f9d1-315">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="3f9d1-315">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="3f9d1-316">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="3f9d1-316">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="3f9d1-317">Переопределение конфигурации</span><span class="sxs-lookup"><span data-stu-id="3f9d1-317">Override configuration</span></span>

<span data-ttu-id="3f9d1-318">Используйте [конфигурацию](xref:fundamentals/configuration/index) для настройки веб-узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-318">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="3f9d1-319">В приведенном ниже примере необязательная конфигурация узла задается в файле *hostsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-319">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="3f9d1-320">Любую конфигурацию, загружаемую из файла *hostsettings.json*, можно переопределить с помощью аргументов командной строки.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-320">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="3f9d1-321">Встроенная конфигурация (в `config`) используется для настройки узла с помощью [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-321">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="3f9d1-322">Конфигурация `IWebHostBuilder` добавляется в конфигурацию приложения, но не наоборот &mdash;`ConfigureAppConfiguration` не влияет на конфигурацию `IWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-322">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="3f9d1-323">Конфигурация, предоставленная методом `UseUrls`, сначала переопределяется конфигурацией из файла *hostsettings.json*, а затем с помощью аргументов командной строки:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-323">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="3f9d1-324">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-324">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="3f9d1-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) копирует ключи только из предоставленного объекта `IConfiguration` в конфигурацию построителя узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-325">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="3f9d1-326">Поэтому указание `reloadOnChange: true` для файлов JSON, XML и INI ни на что не влияет.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-326">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="3f9d1-327">Чтобы указать узел, выполняющийся по определенному URL-адресу, можно передать нужное значение из командной строки при выполнении команды [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-327">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="3f9d1-328">Аргумент командной строки переопределяет значение `urls` из файла *hostsettings.json*, и сервер будет ожидать передачи данных через порт 8080:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-328">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="3f9d1-329">Управление узлом</span><span class="sxs-lookup"><span data-stu-id="3f9d1-329">Manage the host</span></span>

<span data-ttu-id="3f9d1-330">**Выполнить**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-330">**Run**</span></span>

<span data-ttu-id="3f9d1-331">Метод `Run` запускает веб-приложение и блокирует вызывающий поток до тех пор, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-331">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="3f9d1-332">**Запуск**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-332">**Start**</span></span>

<span data-ttu-id="3f9d1-333">Чтобы запустить узел без блокировки, вызовите метод `Start`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-333">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="3f9d1-334">Если в метод `Start` передается список URL-адресов, он будет ожидать передачи данных по указанным URL-адресам.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-334">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="3f9d1-335">Приложение может инициализировать и запустить новый узел с использованием предварительно настроенных значений по умолчанию `CreateDefaultBuilder` с помощью статического удобного метода.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-335">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="3f9d1-336">Эти методы запускают сервер без вывода данных в консоль и со временем ожидания прерывания, равным [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) (Ctrl-C/SIGINT или SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="3f9d1-336">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="3f9d1-337">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-337">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="3f9d1-338">Выполните запуск с помощью `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-338">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="3f9d1-339">В браузере выполните запрос по адресу `http://localhost:5000`, чтобы получить ответ "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="3f9d1-339">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="3f9d1-340">`WaitForShutdown` блокируется, пока не будет создано прерывание (Ctrl-C/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-340">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="3f9d1-341">Приложение выводит сообщение `Console.WriteLine` и ожидает нажатия клавиши, после чего завершает работу.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-341">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="3f9d1-342">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-342">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="3f9d1-343">Выполните запуск с помощью URL-адреса и `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-343">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="3f9d1-344">Результат будет тем же, что и при использовании **Start(RequestDelegate app)** , но приложение отвечает по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-344">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="3f9d1-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-345">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="3f9d1-346">Используйте экземпляр `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) для применения ПО промежуточного слоя маршрутизации:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-346">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="3f9d1-347">В этом примере используйте следующие запросы в браузере:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-347">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="3f9d1-348">Запрос</span><span class="sxs-lookup"><span data-stu-id="3f9d1-348">Request</span></span>                                    | <span data-ttu-id="3f9d1-349">Ответ</span><span class="sxs-lookup"><span data-stu-id="3f9d1-349">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="3f9d1-350">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="3f9d1-350">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="3f9d1-351">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="3f9d1-351">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="3f9d1-352">Вызывает исключение со строкой "ooops!"</span><span class="sxs-lookup"><span data-stu-id="3f9d1-352">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="3f9d1-353">Вызывает исключение со строкой "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="3f9d1-353">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="3f9d1-354">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="3f9d1-354">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="3f9d1-355">Пример "Здравствуй,</span><span class="sxs-lookup"><span data-stu-id="3f9d1-355">Hello World!</span></span>                             |

<span data-ttu-id="3f9d1-356">`WaitForShutdown` блокируется, пока не будет создано прерывание (Ctrl-C/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-356">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="3f9d1-357">Приложение выводит сообщение `Console.WriteLine` и ожидает нажатия клавиши, после чего завершает работу.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-357">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="3f9d1-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-358">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="3f9d1-359">Используйте URL-адрес и экземпляр `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-359">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="3f9d1-360">Результат будет тем же, что и при использовании **Start(Action\<IRouteBuilder> routeBuilder)** , но приложение отвечает по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-360">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="3f9d1-361">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-361">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="3f9d1-362">Предоставьте делегат для настройки `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-362">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="3f9d1-363">В браузере выполните запрос по адресу `http://localhost:5000`, чтобы получить ответ "Hello World!"</span><span class="sxs-lookup"><span data-stu-id="3f9d1-363">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="3f9d1-364">`WaitForShutdown` блокируется, пока не будет создано прерывание (Ctrl-C/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-364">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="3f9d1-365">Приложение выводит сообщение `Console.WriteLine` и ожидает нажатия клавиши, после чего завершает работу.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-365">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="3f9d1-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="3f9d1-366">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="3f9d1-367">Предоставьте URL-адрес и делегат для настройки `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-367">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="3f9d1-368">Результат будет тем же, что и при использовании **StartWith(Action\<IApplicationBuilder> app)** , но приложение отвечает по адресу `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-368">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="3f9d1-369">Интерфейс IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="3f9d1-369">IWebHostEnvironment interface</span></span>

<span data-ttu-id="3f9d1-370">Интерфейс `IWebHostEnvironment` предоставляет сведения о среде веб-размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-370">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="3f9d1-371">Чтобы получить интерфейс `IWebHostEnvironment` для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="3f9d1-371">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="3f9d1-372">Для настройки приложения при запуске в соответствии со средой можно применять [подход на основе соглашения](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-372">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="3f9d1-373">Кроме того, можно внедрить интерфейс `IWebHostEnvironment` в конструктор `Startup` для использования в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-373">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="3f9d1-374">Помимо метода расширения `IsDevelopment`, интерфейс `IWebHostEnvironment` предоставляет методы `IsStaging`, `IsProduction` и `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-374">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="3f9d1-375">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-375">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3f9d1-376">Службу `IWebHostEnvironment` также можно внедрять непосредственно в метод `Configure` для настройки конвейера обработки:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-376">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="3f9d1-377">`IWebHostEnvironment` можно внедрить в метод `Invoke` при создании пользовательского [ПО промежуточного слоя](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="3f9d1-377">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="3f9d1-378">Интерфейс IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="3f9d1-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="3f9d1-379">[Интерфейс IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) предоставляет сведения о среде веб-размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="3f9d1-380">Чтобы получить интерфейс `IHostingEnvironment` для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="3f9d1-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="3f9d1-381">Для настройки приложения при запуске в соответствии со средой можно применять [подход на основе соглашения](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-381">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="3f9d1-382">Кроме того, можно внедрить интерфейс `IHostingEnvironment` в конструктор `Startup` для использования в `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="3f9d1-383">Помимо метода расширения `IsDevelopment`, интерфейс `IHostingEnvironment` предоставляет методы `IsStaging`, `IsProduction` и `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="3f9d1-384">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-384">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3f9d1-385">Службу `IHostingEnvironment` также можно внедрять непосредственно в метод `Configure` для настройки конвейера обработки:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="3f9d1-386">`IHostingEnvironment` можно внедрить в метод `Invoke` при создании пользовательского [ПО промежуточного слоя](xref:fundamentals/middleware/write):</span><span class="sxs-lookup"><span data-stu-id="3f9d1-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="3f9d1-387">Интерфейс IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3f9d1-387">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="3f9d1-388">Интерфейс `IHostApplicationLifetime` позволяет выполнять действия после запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-388">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="3f9d1-389">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов `Action`, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="3f9d1-390">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="3f9d1-390">Cancellation Token</span></span>    | <span data-ttu-id="3f9d1-391">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="3f9d1-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="3f9d1-392">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-392">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="3f9d1-393">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-393">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="3f9d1-394">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-394">All requests should be processed.</span></span> <span data-ttu-id="3f9d1-395">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-395">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="3f9d1-396">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-396">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="3f9d1-397">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-397">Requests may still be processing.</span></span> <span data-ttu-id="3f9d1-398">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-398">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="3f9d1-399">Метод `StopApplication` запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-399">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="3f9d1-400">Следующий класс использует `StopApplication` для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-400">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="3f9d1-401">Интерфейс IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="3f9d1-401">IApplicationLifetime interface</span></span>

<span data-ttu-id="3f9d1-402">[Интерфейс IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) позволяет выполнять действия после запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-402">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="3f9d1-403">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов `Action`, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-403">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="3f9d1-404">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="3f9d1-404">Cancellation Token</span></span>    | <span data-ttu-id="3f9d1-405">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="3f9d1-405">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="3f9d1-406">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="3f9d1-406">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="3f9d1-407">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-407">The host has fully started.</span></span> |
| [<span data-ttu-id="3f9d1-408">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="3f9d1-408">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="3f9d1-409">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-409">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="3f9d1-410">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-410">All requests should be processed.</span></span> <span data-ttu-id="3f9d1-411">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-411">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="3f9d1-412">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="3f9d1-412">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="3f9d1-413">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-413">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="3f9d1-414">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-414">Requests may still be processing.</span></span> <span data-ttu-id="3f9d1-415">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-415">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="3f9d1-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-416">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="3f9d1-417">Следующий класс использует `StopApplication` для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-417">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="3f9d1-418">Проверка области</span><span class="sxs-lookup"><span data-stu-id="3f9d1-418">Scope validation</span></span>

<span data-ttu-id="3f9d1-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) устанавливает для [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) значение `true`, если приложение находится в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-419">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="3f9d1-420">Если для `ValidateScopes` установлено значение `true`, поставщик службы по умолчанию проверяет, что:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-420">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="3f9d1-421">Службы с заданной областью не разрешаются из корневого поставщика службы, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-421">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="3f9d1-422">Службы с заданной областью не вводятся в одноэлементные объекты, прямо или косвенно.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-422">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="3f9d1-423">Корневой поставщик службы создается при вызове [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="3f9d1-423">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="3f9d1-424">Время существования корневого поставщика службы соответствует времени существования приложения или сервера — поставщик запускается с приложением и удаляется, когда приложение завершает работу.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-424">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="3f9d1-425">Службы с заданной областью удаляются создавшим их контейнером.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-425">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="3f9d1-426">Если служба с заданной областью создается в корневом контейнере, время существования службы повышается до уровня одноэлементного объекта, поскольку она удаляется только корневым контейнером при завершении работы приложения или сервера.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-426">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="3f9d1-427">Проверка областей службы перехватывает эти ситуации при вызове `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="3f9d1-427">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="3f9d1-428">Чтобы всегда проверять области, в том числе в рабочей среде, настройте [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) с [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) в конструкторе узлов:</span><span class="sxs-lookup"><span data-stu-id="3f9d1-428">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="3f9d1-429">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3f9d1-429">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
