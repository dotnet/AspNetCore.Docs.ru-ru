---
title: Универсальный узел .NET в ASP.NET Core
author: rick-anderson
description: Используйте универсальный узел .NET в приложениях ASP.NET Core.  Универсальный узел отвечает за запуск приложения и управление временем существования.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: b99b0f0ab6e67ac84bf1232ff6681c5edd54ffb9
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253180"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="e3640-104">Универсальный узел .NET в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e3640-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e3640-105">Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="e3640-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="e3640-106">В этой статье приведены сведения об использовании универсального узла .NET в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3640-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="e3640-107">Сведения об использовании универсального узла .NET в консольных приложениях см. в статье [Универсальный узел .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e3640-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="e3640-108">Определение узла</span><span class="sxs-lookup"><span data-stu-id="e3640-108">Host definition</span></span>

<span data-ttu-id="e3640-109">*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:</span><span class="sxs-lookup"><span data-stu-id="e3640-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e3640-110">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="e3640-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="e3640-111">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="e3640-111">Logging</span></span>
* <span data-ttu-id="e3640-112">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="e3640-112">Configuration</span></span>
* <span data-ttu-id="e3640-113">Реализации `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="e3640-113">`IHostedService` implementations</span></span>

<span data-ttu-id="e3640-114">После запуска узла он вызывает <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированной в коллекции размещенных служб контейнера службы.</span><span class="sxs-lookup"><span data-stu-id="e3640-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="e3640-115">В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="e3640-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e3640-116">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e3640-117">Создание узла</span><span class="sxs-lookup"><span data-stu-id="e3640-117">Set up a host</span></span>

<span data-ttu-id="e3640-118">Узел обычно настраивается, собирается и выполняется кодом в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="e3640-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e3640-119">Метод `Main`:</span><span class="sxs-lookup"><span data-stu-id="e3640-119">The `Main` method:</span></span>

* <span data-ttu-id="e3640-120">Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="e3640-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e3640-121">Вызывает методы `Build` и `Run` в объекте построителя.</span><span class="sxs-lookup"><span data-stu-id="e3640-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e3640-122">Веб-шаблоны ASP.NET Core создают следующий код для создания узла:</span><span class="sxs-lookup"><span data-stu-id="e3640-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="e3640-123">Следующий код создают нагрузку, отличную от HTTP, с одной реализацией `IHostedService`, добавленной в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e3640-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="e3640-124">При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="e3640-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e3640-125">Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e3640-126">[Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="e3640-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e3640-127">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="e3640-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e3640-128">Параметры построителя по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e3640-128">Default builder settings</span></span>

<span data-ttu-id="e3640-129">Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span><span class="sxs-lookup"><span data-stu-id="e3640-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="e3640-130">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="e3640-131">Загружает конфигурацию узла из:</span><span class="sxs-lookup"><span data-stu-id="e3640-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="e3640-132">Переменные среды с префиксом `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e3640-133">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e3640-133">Command-line arguments.</span></span>
* <span data-ttu-id="e3640-134">Загружает конфигурацию приложения из:</span><span class="sxs-lookup"><span data-stu-id="e3640-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="e3640-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e3640-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="e3640-136">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="e3640-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e3640-137">[секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development`;</span><span class="sxs-lookup"><span data-stu-id="e3640-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e3640-138">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-138">Environment variables.</span></span>
  * <span data-ttu-id="e3640-139">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e3640-139">Command-line arguments.</span></span>
* <span data-ttu-id="e3640-140">Добавляет следующие [регистраторы](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="e3640-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e3640-141">Консоль</span><span class="sxs-lookup"><span data-stu-id="e3640-141">Console</span></span>
  * <span data-ttu-id="e3640-142">Отладка</span><span class="sxs-lookup"><span data-stu-id="e3640-142">Debug</span></span>
  * <span data-ttu-id="e3640-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="e3640-143">EventSource</span></span>
  * <span data-ttu-id="e3640-144">Журнал событий (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="e3640-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e3640-145">Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.</span><span class="sxs-lookup"><span data-stu-id="e3640-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e3640-146">Метод <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>:</span><span class="sxs-lookup"><span data-stu-id="e3640-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="e3640-147">Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e3640-148">Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e3640-149">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel/options>.</span><span class="sxs-lookup"><span data-stu-id="e3640-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
* <span data-ttu-id="e3640-150">Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="e3640-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel/host-filtering).</span></span>
* <span data-ttu-id="e3640-151">Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.</span><span class="sxs-lookup"><span data-stu-id="e3640-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e3640-152">Обеспечивает интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="e3640-152">Enables IIS integration.</span></span> <span data-ttu-id="e3640-153">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="e3640-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e3640-154">Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3640-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e3640-155">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="e3640-155">Framework-provided services</span></span>

<span data-ttu-id="e3640-156">Следующие службы регистрируются автоматически.</span><span class="sxs-lookup"><span data-stu-id="e3640-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e3640-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e3640-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e3640-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e3640-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e3640-160">Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="e3640-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e3640-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="e3640-162">Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e3640-163">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e3640-164">Этот интерфейс также включает метод `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="e3640-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e3640-165">Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e3640-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e3640-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-166">IHostLifetime</span></span>

<span data-ttu-id="e3640-167">Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается.</span><span class="sxs-lookup"><span data-stu-id="e3640-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e3640-168">Используется последняя зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="e3640-168">The last implementation registered is used.</span></span>

<span data-ttu-id="e3640-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3640-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e3640-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e3640-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e3640-171">Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="e3640-172">разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="e3640-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e3640-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e3640-173">IHostEnvironment</span></span>

<span data-ttu-id="e3640-174">Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.</span><span class="sxs-lookup"><span data-stu-id="e3640-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e3640-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e3640-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e3640-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e3640-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e3640-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e3640-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="e3640-178">Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="e3640-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e3640-179">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="e3640-179">Host configuration</span></span>

<span data-ttu-id="e3640-180">Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="e3640-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e3640-181">Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="e3640-182">После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e3640-183">Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e3640-184">Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e3640-185">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="e3640-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e3640-186">Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e3640-187">Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e3640-188">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e3640-189">Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="e3640-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e3640-190">В следующем примере создается конфигурация узла:</span><span class="sxs-lookup"><span data-stu-id="e3640-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e3640-191">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="e3640-191">App configuration</span></span>

<span data-ttu-id="e3640-192">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e3640-193">Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e3640-194">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="e3640-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e3640-195">Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e3640-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e3640-196">Конфигурация узла также добавляется к конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e3640-197">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="e3640-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e3640-198">Параметры для всех типов приложений</span><span class="sxs-lookup"><span data-stu-id="e3640-198">Settings for all app types</span></span>

<span data-ttu-id="e3640-199">В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным.</span><span class="sxs-lookup"><span data-stu-id="e3640-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e3640-200">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="e3640-201">Дополнительные сведения см. в разделе [Параметры построителя по умолчанию](#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="e3640-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e3640-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e3640-202">ApplicationName</span></span>

<span data-ttu-id="e3640-203">Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e3640-204">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="e3640-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e3640-205">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-205">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-206">**По умолчанию**: Имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e3640-207">**Переменная среды**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e3640-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e3640-208">Чтобы задать это значение, используйте переменную среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="e3640-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e3640-209">ContentRoot</span></span>

<span data-ttu-id="e3640-210">Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="e3640-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e3640-211">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="e3640-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e3640-212">**Ключ**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e3640-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e3640-213">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-213">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-214">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e3640-215">**Переменная среды**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e3640-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e3640-216">Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e3640-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e3640-217">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="e3640-217">For more information, see:</span></span>

* [<span data-ttu-id="e3640-218">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="e3640-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e3640-219">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="e3640-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e3640-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e3640-220">EnvironmentName</span></span>

<span data-ttu-id="e3640-221">Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение.</span><span class="sxs-lookup"><span data-stu-id="e3640-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e3640-222">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="e3640-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e3640-223">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="e3640-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e3640-224">**Ключ**: `environment`</span><span class="sxs-lookup"><span data-stu-id="e3640-224">**Key**: `environment`</span></span>  
<span data-ttu-id="e3640-225">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-225">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-226">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="e3640-226">**Default**: `Production`</span></span>  
<span data-ttu-id="e3640-227">**Переменная среды**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e3640-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e3640-228">Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e3640-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e3640-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e3640-229">ShutdownTimeout</span></span>

<span data-ttu-id="e3640-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e3640-231">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="e3640-231">The default value is five seconds.</span></span>  <span data-ttu-id="e3640-232">Во время ожидания узел:</span><span class="sxs-lookup"><span data-stu-id="e3640-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="e3640-233">Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="e3640-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e3640-234">Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="e3640-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e3640-235">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e3640-236">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="e3640-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e3640-237">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="e3640-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e3640-238">**Ключ**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e3640-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e3640-239">**Тип**: `int`</span><span class="sxs-lookup"><span data-stu-id="e3640-239">**Type**: `int`</span></span>  
<span data-ttu-id="e3640-240">**По умолчанию**: 5 секунд</span><span class="sxs-lookup"><span data-stu-id="e3640-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e3640-241">**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e3640-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e3640-242">Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="e3640-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e3640-243">В следующем примере устанавливается время ожидания в 20 секунд:</span><span class="sxs-lookup"><span data-stu-id="e3640-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="e3640-244">Отключение перезагрузки конфигурации приложения при изменении</span><span class="sxs-lookup"><span data-stu-id="e3640-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="e3640-245">[По умолчанию](xref:fundamentals/configuration/index#default) при изменении файла выполняется перезагрузка *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="e3640-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="e3640-246">Чтобы отключить эту функцию перезагрузки в ASP.NET Core 5.0 или более поздней версии, присвойте ключу `hostBuilder:reloadConfigOnChange` значение `false`.</span><span class="sxs-lookup"><span data-stu-id="e3640-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="e3640-247">**Ключ**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e3640-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e3640-248">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-249">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="e3640-249">**Default**: `true`</span></span>  
<span data-ttu-id="e3640-250">**Аргумент командной строки**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e3640-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="e3640-251">**Переменная среды**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="e3640-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="e3640-252">Двоеточие (`:`) не работает в качестве разделителя с иерархическими ключами переменных среды на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="e3640-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="e3640-253">Дополнительную информацию см. в разделе [Переменные среды](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="e3640-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="e3640-254">Параметры для веб-приложений</span><span class="sxs-lookup"><span data-stu-id="e3640-254">Settings for web apps</span></span>

<span data-ttu-id="e3640-255">Некоторые параметры узла применяются только к рабочим нагрузкам HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3640-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e3640-256">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e3640-257">Методы расширения в `IWebHostBuilder` доступны для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="e3640-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e3640-258">В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e3640-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e3640-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e3640-259">CaptureStartupErrors</span></span>

<span data-ttu-id="e3640-260">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e3640-261">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="e3640-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e3640-262">**Ключ**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e3640-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e3640-263">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-264">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="e3640-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e3640-265">**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e3640-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e3640-266">Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="e3640-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e3640-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e3640-267">DetailedErrors</span></span>

<span data-ttu-id="e3640-268">Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.</span><span class="sxs-lookup"><span data-stu-id="e3640-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e3640-269">**Ключ**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e3640-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e3640-270">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-271">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="e3640-271">**Default**: `false`</span></span>  
<span data-ttu-id="e3640-272">**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e3640-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e3640-273">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e3640-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e3640-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="e3640-275">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="e3640-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e3640-276">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e3640-277">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="e3640-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e3640-278">**Ключ**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e3640-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e3640-279">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-279">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-280">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="e3640-280">**Default**: Empty string</span></span>  
<span data-ttu-id="e3640-281">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e3640-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e3640-282">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e3640-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e3640-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e3640-284">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="e3640-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e3640-285">**Ключ**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e3640-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e3640-286">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-286">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-287">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="e3640-287">**Default**: Empty string</span></span>  
<span data-ttu-id="e3640-288">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e3640-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e3640-289">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e3640-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e3640-290">HTTPS_Port</span></span>

<span data-ttu-id="e3640-291">Порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e3640-291">The HTTPS redirect port.</span></span> <span data-ttu-id="e3640-292">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e3640-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e3640-293">**Ключ**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="e3640-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="e3640-294">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-294">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-295">**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="e3640-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e3640-296">**Переменная среды**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e3640-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e3640-297">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e3640-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="e3640-298">PreferHostingUrls</span></span>

<span data-ttu-id="e3640-299">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="e3640-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e3640-300">**Ключ**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e3640-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e3640-301">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-302">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="e3640-302">**Default**: `true`</span></span>  
<span data-ttu-id="e3640-303">**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e3640-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e3640-304">Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="e3640-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e3640-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="e3640-305">PreventHostingStartup</span></span>

<span data-ttu-id="e3640-306">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e3640-307">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e3640-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e3640-308">**Ключ**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e3640-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e3640-309">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-310">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="e3640-310">**Default**: `false`</span></span>  
<span data-ttu-id="e3640-311">**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e3640-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e3640-312">Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e3640-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e3640-313">StartupAssembly</span></span>

<span data-ttu-id="e3640-314">Сборка, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e3640-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e3640-315">**Ключ**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e3640-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e3640-316">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-316">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-317">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="e3640-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e3640-318">**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e3640-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e3640-319">Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="e3640-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e3640-320">`UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="e3640-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e3640-321">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="e3640-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e3640-322">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="e3640-322">URLs</span></span>

<span data-ttu-id="e3640-323">Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="e3640-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e3640-324">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="e3640-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e3640-325">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="e3640-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e3640-326">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="e3640-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e3640-327">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="e3640-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="e3640-328">**Ключ**: `urls`</span><span class="sxs-lookup"><span data-stu-id="e3640-328">**Key**: `urls`</span></span>  
<span data-ttu-id="e3640-329">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-329">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-330">**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e3640-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e3640-331">**Переменная среды**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e3640-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e3640-332">Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="e3640-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e3640-333">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="e3640-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e3640-334">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel/endpoints>.</span><span class="sxs-lookup"><span data-stu-id="e3640-334">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e3640-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e3640-335">WebRoot</span></span>

<span data-ttu-id="e3640-336">Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="e3640-337">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="e3640-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="e3640-338">**Ключ:** `webroot`</span><span class="sxs-lookup"><span data-stu-id="e3640-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="e3640-339">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-339">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-340">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="e3640-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e3640-341">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="e3640-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="e3640-342">**Переменная среды**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e3640-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e3640-343">Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e3640-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e3640-344">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="e3640-344">For more information, see:</span></span>

* [<span data-ttu-id="e3640-345">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="e3640-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e3640-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e3640-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e3640-347">Управление временем существования узла</span><span class="sxs-lookup"><span data-stu-id="e3640-347">Manage the host lifetime</span></span>

<span data-ttu-id="e3640-348">Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e3640-349">Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="e3640-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e3640-350">Выполнить</span><span class="sxs-lookup"><span data-stu-id="e3640-350">Run</span></span>

<span data-ttu-id="e3640-351">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="e3640-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e3640-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-352">RunAsync</span></span>

<span data-ttu-id="e3640-353">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e3640-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-354">RunConsoleAsync</span></span>

<span data-ttu-id="e3640-355">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e3640-356">Начало</span><span class="sxs-lookup"><span data-stu-id="e3640-356">Start</span></span>

<span data-ttu-id="e3640-357">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="e3640-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e3640-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-358">StartAsync</span></span>

<span data-ttu-id="e3640-359">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e3640-360">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="e3640-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e3640-361">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="e3640-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e3640-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-362">StopAsync</span></span>

<span data-ttu-id="e3640-363">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="e3640-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e3640-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e3640-364">WaitForShutdown</span></span>

<span data-ttu-id="e3640-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="e3640-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e3640-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="e3640-367">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e3640-368">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="e3640-368">External control</span></span>

<span data-ttu-id="e3640-369">Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="e3640-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e3640-370">Шаблоны ASP.NET Core создают .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span><span class="sxs-lookup"><span data-stu-id="e3640-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="e3640-371">В этой статье приведены сведения об использовании универсального узла .NET в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3640-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="e3640-372">Сведения об использовании универсального узла .NET в консольных приложениях см. в статье [Универсальный узел .NET](/dotnet/core/extensions/generic-host).</span><span class="sxs-lookup"><span data-stu-id="e3640-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="e3640-373">Определение узла</span><span class="sxs-lookup"><span data-stu-id="e3640-373">Host definition</span></span>

<span data-ttu-id="e3640-374">*Узел* — это объект, который инкапсулирует все ресурсы приложения, такие как:</span><span class="sxs-lookup"><span data-stu-id="e3640-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="e3640-375">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="e3640-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="e3640-376">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="e3640-376">Logging</span></span>
* <span data-ttu-id="e3640-377">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="e3640-377">Configuration</span></span>
* <span data-ttu-id="e3640-378">Реализации `IHostedService`</span><span class="sxs-lookup"><span data-stu-id="e3640-378">`IHostedService` implementations</span></span>

<span data-ttu-id="e3640-379">После запуска узла он вызывает <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> в каждой реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированной в коллекции размещенных служб контейнера службы.</span><span class="sxs-lookup"><span data-stu-id="e3640-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="e3640-380">В веб-приложении одна из реализаций `IHostedService` является веб-службой, которая запускает [реализацию сервера HTTP](xref:fundamentals/index#servers).</span><span class="sxs-lookup"><span data-stu-id="e3640-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="e3640-381">Основной причиной включения всех взаимозависимых ресурсов приложения в один объект является управление жизненным циклом: контроль запуска и корректного завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e3640-382">Создание узла</span><span class="sxs-lookup"><span data-stu-id="e3640-382">Set up a host</span></span>

<span data-ttu-id="e3640-383">Узел обычно настраивается, собирается и выполняется кодом в классе `Program`.</span><span class="sxs-lookup"><span data-stu-id="e3640-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="e3640-384">Метод `Main`:</span><span class="sxs-lookup"><span data-stu-id="e3640-384">The `Main` method:</span></span>

* <span data-ttu-id="e3640-385">Вызывает метод `CreateHostBuilder` для создания и настройки объекта построителя.</span><span class="sxs-lookup"><span data-stu-id="e3640-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="e3640-386">Вызывает методы `Build` и `Run` в объекте построителя.</span><span class="sxs-lookup"><span data-stu-id="e3640-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="e3640-387">Веб-шаблоны ASP.NET Core создают следующий код для создания универсального узла:</span><span class="sxs-lookup"><span data-stu-id="e3640-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="e3640-388">Следующий код создает универсальный узел, используя рабочую нагрузку, отличную от HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3640-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="e3640-389">Реализация `IHostedService` добавляется в контейнер DI:</span><span class="sxs-lookup"><span data-stu-id="e3640-389">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="e3640-390">При использовании рабочей нагрузки HTTP метод `Main` остается прежним, но `CreateHostBuilder` вызывает `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="e3640-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="e3640-391">Приведенный выше код создается шаблонами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e3640-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="e3640-392">Если приложение использует Entity Framework Core, не изменяйте имя или сигнатуру метода `CreateHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="e3640-393">[Инструменты Entity Framework Core](/ef/core/miscellaneous/cli/) ищут метод `CreateHostBuilder`, который настраивает узел без необходимости запускать приложение.</span><span class="sxs-lookup"><span data-stu-id="e3640-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="e3640-394">Подробные сведения см. в статье [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation) (Создание экземпляра DbContext во время разработки).</span><span class="sxs-lookup"><span data-stu-id="e3640-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="e3640-395">Параметры построителя по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e3640-395">Default builder settings</span></span>

<span data-ttu-id="e3640-396">Метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>:</span><span class="sxs-lookup"><span data-stu-id="e3640-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="e3640-397">В качестве [корневого каталога содержимого](xref:fundamentals/index#content-root) задает путь, возвращенный методом <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span><span class="sxs-lookup"><span data-stu-id="e3640-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="e3640-398">Загружает конфигурацию узла из:</span><span class="sxs-lookup"><span data-stu-id="e3640-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="e3640-399">Переменные среды с префиксом `DOTNET_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="e3640-400">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e3640-400">Command-line arguments.</span></span>
* <span data-ttu-id="e3640-401">Загружает конфигурацию приложения из:</span><span class="sxs-lookup"><span data-stu-id="e3640-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="e3640-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e3640-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="e3640-403">*appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="e3640-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="e3640-404">[секреты пользователя](xref:security/app-secrets), когда приложение выполняется в среде `Development`;</span><span class="sxs-lookup"><span data-stu-id="e3640-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="e3640-405">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-405">Environment variables.</span></span>
  * <span data-ttu-id="e3640-406">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="e3640-406">Command-line arguments.</span></span>
* <span data-ttu-id="e3640-407">Добавляет следующие [регистраторы](xref:fundamentals/logging/index):</span><span class="sxs-lookup"><span data-stu-id="e3640-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="e3640-408">Консоль</span><span class="sxs-lookup"><span data-stu-id="e3640-408">Console</span></span>
  * <span data-ttu-id="e3640-409">Отладка</span><span class="sxs-lookup"><span data-stu-id="e3640-409">Debug</span></span>
  * <span data-ttu-id="e3640-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="e3640-410">EventSource</span></span>
  * <span data-ttu-id="e3640-411">Журнал событий (только при запуске в Windows)</span><span class="sxs-lookup"><span data-stu-id="e3640-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="e3640-412">Включает [проверку области](xref:fundamentals/dependency-injection#scope-validation) и [проверку зависимостей](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild), если это среда разработки.</span><span class="sxs-lookup"><span data-stu-id="e3640-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="e3640-413">Метод `ConfigureWebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="e3640-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="e3640-414">Загружает конфигурацию узла из переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="e3640-415">Задает сервер [Kestrel](xref:fundamentals/servers/kestrel) в качестве веб-сервера и настраивает его с помощью поставщиков конфигурации размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="e3640-416">Параметры сервера Kestrel по умолчанию см. в разделе <xref:fundamentals/servers/kestrel#kestrel-options>.</span><span class="sxs-lookup"><span data-stu-id="e3640-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="e3640-417">Добавляет [ПО промежуточного слоя фильтрации узлов](xref:fundamentals/servers/kestrel#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="e3640-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="e3640-418">Добавляет [Параметры ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers), если `ASPNETCORE_FORWARDEDHEADERS_ENABLED` равно `true`.</span><span class="sxs-lookup"><span data-stu-id="e3640-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="e3640-419">Обеспечивает интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="e3640-419">Enables IIS integration.</span></span> <span data-ttu-id="e3640-420">Параметры IIS по умолчанию см. в разделе <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="e3640-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="e3640-421">Разделы [Параметры для всех типов приложений](#settings-for-all-app-types) и [Параметры для веб-приложений](#settings-for-web-apps) далее в этой статье описывают, как переопределить параметры построителя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3640-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="e3640-422">Платформенные службы</span><span class="sxs-lookup"><span data-stu-id="e3640-422">Framework-provided services</span></span>

<span data-ttu-id="e3640-423">Следующие службы регистрируются автоматически.</span><span class="sxs-lookup"><span data-stu-id="e3640-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="e3640-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="e3640-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="e3640-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e3640-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="e3640-427">Дополнительные сведения о службах, предоставляемых платформой, см. в разделе <xref:fundamentals/dependency-injection#framework-provided-services>.</span><span class="sxs-lookup"><span data-stu-id="e3640-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="e3640-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="e3640-429">Внедрите <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (прежнее название — `IApplicationLifetime`) в любой класс для выполнения задач после запуска и корректного завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="e3640-430">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов обработчика событий запуска и завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="e3640-431">Этот интерфейс также включает метод `StopApplication`.</span><span class="sxs-lookup"><span data-stu-id="e3640-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="e3640-432">Ниже приведен пример реализации `IHostedService`, которая регистрирует события `IHostApplicationLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e3640-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="e3640-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-433">IHostLifetime</span></span>

<span data-ttu-id="e3640-434">Реализация <xref:Microsoft.Extensions.Hosting.IHostLifetime> контролирует, когда узел запускается и останавливается.</span><span class="sxs-lookup"><span data-stu-id="e3640-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="e3640-435">Используется последняя зарегистрированная реализация.</span><span class="sxs-lookup"><span data-stu-id="e3640-435">The last implementation registered is used.</span></span>

<span data-ttu-id="e3640-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` — это реализация `IHostLifetime` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3640-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="e3640-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="e3640-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="e3640-438">Прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="e3640-439">разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="e3640-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="e3640-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="e3640-440">IHostEnvironment</span></span>

<span data-ttu-id="e3640-441">Внедряет службу <xref:Microsoft.Extensions.Hosting.IHostEnvironment> в класс, чтобы получить сведения о следующих параметрах.</span><span class="sxs-lookup"><span data-stu-id="e3640-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="e3640-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e3640-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="e3640-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e3640-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="e3640-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="e3640-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="e3640-445">Веб-приложения реализуют интерфейс `IWebHostEnvironment`, который наследует `IHostEnvironment` и добавляет [WebRootPath](#webroot).</span><span class="sxs-lookup"><span data-stu-id="e3640-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e3640-446">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="e3640-446">Host configuration</span></span>

<span data-ttu-id="e3640-447">Конфигурация узла используется для свойств реализации <xref:Microsoft.Extensions.Hosting.IHostEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="e3640-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="e3640-448">Конфигурация узла доступна из [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) внутри <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span><span class="sxs-lookup"><span data-stu-id="e3640-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="e3640-449">После `ConfigureAppConfiguration``HostBuilderContext.Configuration` заменяется конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="e3640-450">Чтобы добавить конфигурацию узла, вызовите <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="e3640-451">Метод `ConfigureHostConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e3640-452">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="e3640-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e3640-453">Поставщик переменных среды с префиксом `DOTNET_` и аргументы командной строки включены в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="e3640-454">Для веб-приложений добавляется поставщик переменных среды с префиксом `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="e3640-455">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e3640-456">Например, значение переменной среды для `ASPNETCORE_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="e3640-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e3640-457">В следующем примере создается конфигурация узла:</span><span class="sxs-lookup"><span data-stu-id="e3640-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="e3640-458">Конфигурация приложения</span><span class="sxs-lookup"><span data-stu-id="e3640-458">App configuration</span></span>

<span data-ttu-id="e3640-459">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в `IHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="e3640-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="e3640-460">Метод `ConfigureAppConfiguration` может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="e3640-461">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="e3640-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="e3640-462">Конфигурация, созданная с помощью `ConfigureAppConfiguration`, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и как служба из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e3640-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="e3640-463">Конфигурация узла также добавляется к конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="e3640-464">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span><span class="sxs-lookup"><span data-stu-id="e3640-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="e3640-465">Параметры для всех типов приложений</span><span class="sxs-lookup"><span data-stu-id="e3640-465">Settings for all app types</span></span>

<span data-ttu-id="e3640-466">В этом разделе перечислены параметры узла, которые применяются к рабочим нагрузкам HTTP и остальным.</span><span class="sxs-lookup"><span data-stu-id="e3640-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="e3640-467">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="e3640-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="e3640-468">ApplicationName</span></span>

<span data-ttu-id="e3640-469">Свойство [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="e3640-470">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="e3640-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e3640-471">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-471">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-472">**По умолчанию**: Имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="e3640-473">**Переменная среды**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="e3640-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="e3640-474">Чтобы задать это значение, используйте переменную среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="e3640-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e3640-475">ContentRoot</span></span>

<span data-ttu-id="e3640-476">Свойство [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) определяет, где узел начинает поиск файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="e3640-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="e3640-477">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="e3640-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="e3640-478">**Ключ**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e3640-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e3640-479">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-479">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-480">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="e3640-481">**Переменная среды**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="e3640-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="e3640-482">Чтобы задать это значение, используйте переменную среды или вызов `UseContentRoot` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e3640-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="e3640-483">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="e3640-483">For more information, see:</span></span>

* [<span data-ttu-id="e3640-484">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="e3640-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="e3640-485">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="e3640-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="e3640-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="e3640-486">EnvironmentName</span></span>

<span data-ttu-id="e3640-487">Свойству [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) может быть присвоено любое значение.</span><span class="sxs-lookup"><span data-stu-id="e3640-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="e3640-488">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="e3640-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e3640-489">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="e3640-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="e3640-490">**Ключ**: `environment`</span><span class="sxs-lookup"><span data-stu-id="e3640-490">**Key**: `environment`</span></span>  
<span data-ttu-id="e3640-491">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-491">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-492">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="e3640-492">**Default**: `Production`</span></span>  
<span data-ttu-id="e3640-493">**Переменная среды**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="e3640-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="e3640-494">Чтобы задать это значение, используйте переменную среды или вызов `UseEnvironment` в `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e3640-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="e3640-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="e3640-495">ShutdownTimeout</span></span>

<span data-ttu-id="e3640-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e3640-497">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="e3640-497">The default value is five seconds.</span></span>  <span data-ttu-id="e3640-498">Во время ожидания узел:</span><span class="sxs-lookup"><span data-stu-id="e3640-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="e3640-499">Активирует [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="e3640-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="e3640-500">Пытается остановить размещенные службы, записывая в журнал ошибки для служб, которые не удалось остановить.</span><span class="sxs-lookup"><span data-stu-id="e3640-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="e3640-501">Если время ожидания истекает до остановки всех размещенных служб, активные службы останавливаются при завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="e3640-502">Службы останавливаются даже в том случае, если еще не завершили обработку.</span><span class="sxs-lookup"><span data-stu-id="e3640-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="e3640-503">Если службе требуется дополнительное время для остановки, увеличьте время ожидания.</span><span class="sxs-lookup"><span data-stu-id="e3640-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="e3640-504">**Ключ**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="e3640-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="e3640-505">**Тип**: `int`</span><span class="sxs-lookup"><span data-stu-id="e3640-505">**Type**: `int`</span></span>  
<span data-ttu-id="e3640-506">**По умолчанию**: 5 секунд</span><span class="sxs-lookup"><span data-stu-id="e3640-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="e3640-507">**Переменная среды**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="e3640-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="e3640-508">Чтобы задать это значение, используйте переменную среды или настройте `HostOptions`.</span><span class="sxs-lookup"><span data-stu-id="e3640-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="e3640-509">В следующем примере устанавливается время ожидания в 20 секунд:</span><span class="sxs-lookup"><span data-stu-id="e3640-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="e3640-510">Параметры для веб-приложений</span><span class="sxs-lookup"><span data-stu-id="e3640-510">Settings for web apps</span></span>

<span data-ttu-id="e3640-511">Некоторые параметры узла применяются только к рабочим нагрузкам HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3640-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="e3640-512">По умолчанию переменные среды, используемые для настройки этих параметров, могут иметь префикс `DOTNET_` или `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="e3640-513">Методы расширения в `IWebHostBuilder` доступны для этих параметров.</span><span class="sxs-lookup"><span data-stu-id="e3640-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="e3640-514">В примерах кода, которые показывают, как вызывать методы расширения, предполагается, что `webBuilder` является экземпляром `IWebHostBuilder`, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="e3640-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="e3640-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="e3640-515">CaptureStartupErrors</span></span>

<span data-ttu-id="e3640-516">Если задано значение `false`, ошибки во время запуска приводят к завершению работы узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="e3640-517">Если задано значение `true`, узел перехватывает исключения во время запуска и пытается запустить сервер.</span><span class="sxs-lookup"><span data-stu-id="e3640-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="e3640-518">**Ключ**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="e3640-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="e3640-519">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-520">**По умолчанию**: `false`, если только приложение не работает с сервером Kestrel за службами IIS; в этом случае значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="e3640-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="e3640-521">**Переменная среды**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="e3640-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="e3640-522">Чтобы задать это значение, используйте конфигурацию или вызов `CaptureStartupErrors`:</span><span class="sxs-lookup"><span data-stu-id="e3640-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="e3640-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="e3640-523">DetailedErrors</span></span>

<span data-ttu-id="e3640-524">Если этот параметр включен или если среда имеет значение `Development`, приложение перехватывает подробные ошибки.</span><span class="sxs-lookup"><span data-stu-id="e3640-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="e3640-525">**Ключ**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="e3640-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="e3640-526">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-527">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="e3640-527">**Default**: `false`</span></span>  
<span data-ttu-id="e3640-528">**Переменная среды**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="e3640-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="e3640-529">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="e3640-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="e3640-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="e3640-531">Разделенная точками с запятой строка начальных сборок размещения, загружаемых при запуске.</span><span class="sxs-lookup"><span data-stu-id="e3640-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="e3640-532">Хотя значением по умолчанию этого параметра конфигурации является пустая строка, начальные сборки размещения всегда включают в себя сборку приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="e3640-533">Если начальные сборки размещения указаны, они добавляются к сборке приложения для загрузки во время построения приложением общих служб при запуске.</span><span class="sxs-lookup"><span data-stu-id="e3640-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="e3640-534">**Ключ**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e3640-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="e3640-535">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-535">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-536">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="e3640-536">**Default**: Empty string</span></span>  
<span data-ttu-id="e3640-537">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e3640-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="e3640-538">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="e3640-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="e3640-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="e3640-540">Разделенная точками с запятой строка начальных сборок размещения, которые необходимо исключить при запуске.</span><span class="sxs-lookup"><span data-stu-id="e3640-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="e3640-541">**Ключ**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="e3640-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="e3640-542">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-542">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-543">**По умолчанию**: Пустая строка</span><span class="sxs-lookup"><span data-stu-id="e3640-543">**Default**: Empty string</span></span>  
<span data-ttu-id="e3640-544">**Переменная среды**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="e3640-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="e3640-545">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="e3640-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="e3640-546">HTTPS_Port</span></span>

<span data-ttu-id="e3640-547">Порт перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e3640-547">The HTTPS redirect port.</span></span> <span data-ttu-id="e3640-548">Используется при [принудительном применении HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="e3640-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="e3640-549">**Ключ**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="e3640-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="e3640-550">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-550">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-551">**По умолчанию**: значение по умолчанию не задано.</span><span class="sxs-lookup"><span data-stu-id="e3640-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="e3640-552">**Переменная среды**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="e3640-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="e3640-553">Чтобы задать это значение, используйте конфигурацию или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="e3640-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="e3640-554">PreferHostingUrls</span></span>

<span data-ttu-id="e3640-555">Указывает, должен ли узел ожидать передачи данных по URL-адресам, настроенным с помощью `IWebHostBuilder`, вместо URL-адресов, настроенных с помощью реализации `IServer`.</span><span class="sxs-lookup"><span data-stu-id="e3640-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="e3640-556">**Ключ**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="e3640-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="e3640-557">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-558">**По умолчанию**: `true`</span><span class="sxs-lookup"><span data-stu-id="e3640-558">**Default**: `true`</span></span>  
<span data-ttu-id="e3640-559">**Переменная среды**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="e3640-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="e3640-560">Чтобы задать это значение, используйте переменную среды или вызов `PreferHostingUrls`:</span><span class="sxs-lookup"><span data-stu-id="e3640-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="e3640-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="e3640-561">PreventHostingStartup</span></span>

<span data-ttu-id="e3640-562">Запрещает автоматическую загрузку начальных сборок размещения, включая начальные сборки размещения, настроенные сборкой приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="e3640-563">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e3640-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="e3640-564">**Ключ**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="e3640-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="e3640-565">**Тип**: `bool` (`true` или `1`)</span><span class="sxs-lookup"><span data-stu-id="e3640-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="e3640-566">**По умолчанию**: `false`</span><span class="sxs-lookup"><span data-stu-id="e3640-566">**Default**: `false`</span></span>  
<span data-ttu-id="e3640-567">**Переменная среды**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="e3640-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="e3640-568">Чтобы задать это значение, используйте переменную среды или вызов `UseSetting`:</span><span class="sxs-lookup"><span data-stu-id="e3640-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="e3640-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="e3640-569">StartupAssembly</span></span>

<span data-ttu-id="e3640-570">Сборка, в которой необходимо искать класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="e3640-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="e3640-571">**Ключ**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="e3640-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="e3640-572">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-572">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-573">**По умолчанию**: сборка приложения</span><span class="sxs-lookup"><span data-stu-id="e3640-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="e3640-574">**Переменная среды**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="e3640-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="e3640-575">Чтобы задать это значение, используйте переменную среды или вызов `UseStartup`.</span><span class="sxs-lookup"><span data-stu-id="e3640-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="e3640-576">`UseStartup` может использовать имя сборки (`string`) или тип (`TStartup`).</span><span class="sxs-lookup"><span data-stu-id="e3640-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="e3640-577">При вызове нескольких методов `UseStartup` приоритет имеет последний.</span><span class="sxs-lookup"><span data-stu-id="e3640-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="e3640-578">URL-адреса</span><span class="sxs-lookup"><span data-stu-id="e3640-578">URLs</span></span>

<span data-ttu-id="e3640-579">Разделенный точками с запятой список IP-адресов или адресов узлов с портами и протоколами, по которым сервер должен ожидать получения запросов.</span><span class="sxs-lookup"><span data-stu-id="e3640-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="e3640-580">Например, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="e3640-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="e3640-581">Используйте символ "\*", чтобы указать, что сервер должен ожидать получения запросов через определенный порт и по определенному протоколу по любому IP-адресу или имени узла (например, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="e3640-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="e3640-582">Протокол (`http://` или `https://`) должен указываться для каждого URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="e3640-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="e3640-583">Поддерживаемые форматы зависят от сервера.</span><span class="sxs-lookup"><span data-stu-id="e3640-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="e3640-584">**Ключ**: `urls`</span><span class="sxs-lookup"><span data-stu-id="e3640-584">**Key**: `urls`</span></span>  
<span data-ttu-id="e3640-585">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-585">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-586">**Значения по умолчанию**: `http://localhost:5000` и `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="e3640-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="e3640-587">**Переменная среды**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="e3640-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="e3640-588">Чтобы задать это значение, используйте переменную среды или вызов `UseUrls`:</span><span class="sxs-lookup"><span data-stu-id="e3640-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="e3640-589">Kestrel имеет собственный интерфейс API настройки конечных точек.</span><span class="sxs-lookup"><span data-stu-id="e3640-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="e3640-590">Для получения дополнительной информации см. <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e3640-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="e3640-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="e3640-591">WebRoot</span></span>

<span data-ttu-id="e3640-592">Свойство [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) определяет относительный путь к статическим ресурсам приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="e3640-593">Если этот путь не существует, используется фиктивный поставщик файлов.</span><span class="sxs-lookup"><span data-stu-id="e3640-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="e3640-594">**Ключ:** `webroot`</span><span class="sxs-lookup"><span data-stu-id="e3640-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="e3640-595">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-595">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-596">**По умолчанию**: Значение по умолчанию — `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="e3640-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="e3640-597">Наличие пути *{корневой_каталог_содержимого}/wwwroot* обязательно.</span><span class="sxs-lookup"><span data-stu-id="e3640-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="e3640-598">**Переменная среды**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="e3640-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="e3640-599">Чтобы задать это значение, используйте переменную среды или вызов `UseWebRoot` в `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="e3640-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="e3640-600">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="e3640-600">For more information, see:</span></span>

* [<span data-ttu-id="e3640-601">Корневой каталог документов</span><span class="sxs-lookup"><span data-stu-id="e3640-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="e3640-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="e3640-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="e3640-603">Управление временем существования узла</span><span class="sxs-lookup"><span data-stu-id="e3640-603">Manage the host lifetime</span></span>

<span data-ttu-id="e3640-604">Вызывает методы в реализации <xref:Microsoft.Extensions.Hosting.IHost> для запуска и остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="e3640-605">Эти методы влияют на все реализации <xref:Microsoft.Extensions.Hosting.IHostedService>, зарегистрированные в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="e3640-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e3640-606">Выполнить</span><span class="sxs-lookup"><span data-stu-id="e3640-606">Run</span></span>

<span data-ttu-id="e3640-607">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена.</span><span class="sxs-lookup"><span data-stu-id="e3640-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="e3640-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-608">RunAsync</span></span>

<span data-ttu-id="e3640-609">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="e3640-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-610">RunConsoleAsync</span></span>

<span data-ttu-id="e3640-611">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="e3640-612">Начало</span><span class="sxs-lookup"><span data-stu-id="e3640-612">Start</span></span>

<span data-ttu-id="e3640-613">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="e3640-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="e3640-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-614">StartAsync</span></span>

<span data-ttu-id="e3640-615">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает узел и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="e3640-616">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале `StartAsync`, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="e3640-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e3640-617">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="e3640-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="e3640-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-618">StopAsync</span></span>

<span data-ttu-id="e3640-619">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="e3640-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="e3640-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e3640-620">WaitForShutdown</span></span>

<span data-ttu-id="e3640-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> блокирует вызывающий поток до завершения работы, активированного IHostLifetime, например через <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM.</span><span class="sxs-lookup"><span data-stu-id="e3640-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="e3640-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="e3640-623">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="e3640-624">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="e3640-624">External control</span></span>

<span data-ttu-id="e3640-625">Прямое управление временем существования узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="e3640-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e3640-626">Приложения ASP.NET Core настраивают и запускают узел.</span><span class="sxs-lookup"><span data-stu-id="e3640-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="e3640-627">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="e3640-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="e3640-628">В этой статье рассматривается универсальный узел ASP.NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>), используемый для приложений, которые не обрабатывают запросы HTTP.</span><span class="sxs-lookup"><span data-stu-id="e3640-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="e3640-629">Универсальный узел предназначен для отделения конвейера HTTP от API веб-узла, чтобы можно было иметь больше сценариев узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="e3640-630">Обмен сообщениями, фоновые задачи и другие рабочие нагрузки, не связанные с HTTP, используют перекрестные возможности универсальных узлов, такие как конфигурация, внедрение зависимости (DI) и ведение журналов.</span><span class="sxs-lookup"><span data-stu-id="e3640-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="e3640-631">Универсальный узел является новой возможностью ASP.NET Core 2.1 и не подходит для сценариев с веб-размещением.</span><span class="sxs-lookup"><span data-stu-id="e3640-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="e3640-632">Сведения о сценариях с веб-размещением см. в статье о [веб-узле](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="e3640-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="e3640-633">Универсальный узел заменит веб-узел в будущих версиях. Он будет выступать основным узлом API для сценариев HTTP и "не HTTP".</span><span class="sxs-lookup"><span data-stu-id="e3640-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="e3640-634">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e3640-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e3640-635">При выполнении примера приложения в [Visual Studio Code](https://code.visualstudio.com/) используйте *внешний или встроенный терминал*.</span><span class="sxs-lookup"><span data-stu-id="e3640-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="e3640-636">Не выполняйте пример в `internalConsole`.</span><span class="sxs-lookup"><span data-stu-id="e3640-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="e3640-637">Настройка консоли в Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="e3640-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="e3640-638">Откройте файл *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="e3640-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="e3640-639">В разделе конфигурации **.NET Core Launch (console)** найдите параметр **console**.</span><span class="sxs-lookup"><span data-stu-id="e3640-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="e3640-640">Измените значение на `externalTerminal` или `integratedTerminal`.</span><span class="sxs-lookup"><span data-stu-id="e3640-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="e3640-641">Вступление</span><span class="sxs-lookup"><span data-stu-id="e3640-641">Introduction</span></span>

<span data-ttu-id="e3640-642">Библиотека универсального узла находится в пространстве имен <xref:Microsoft.Extensions.Hosting> и включена в пакет [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/).</span><span class="sxs-lookup"><span data-stu-id="e3640-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="e3640-643">Пакет `Microsoft.Extensions.Hosting` входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="e3640-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="e3640-644"><xref:Microsoft.Extensions.Hosting.IHostedService> — это точка входа для выполнения кода.</span><span class="sxs-lookup"><span data-stu-id="e3640-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="e3640-645">Каждая реализация <xref:Microsoft.Extensions.Hosting.IHostedService> выполняется в порядке [регистрации служб в ConfigureServices](#configureservices).</span><span class="sxs-lookup"><span data-stu-id="e3640-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="e3640-646">Метод <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> вызывается в каждом <xref:Microsoft.Extensions.Hosting.IHostedService> при запуске узла, а метод <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> вызывается в порядке, обратном регистрации, когда узел корректно завершает работу.</span><span class="sxs-lookup"><span data-stu-id="e3640-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="e3640-647">Создание узла</span><span class="sxs-lookup"><span data-stu-id="e3640-647">Set up a host</span></span>

<span data-ttu-id="e3640-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> является основным компонентом, который библиотеки и приложения используют для инициализации, построения и запуска узла:</span><span class="sxs-lookup"><span data-stu-id="e3640-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="e3640-649">Параметры</span><span class="sxs-lookup"><span data-stu-id="e3640-649">Options</span></span>

<span data-ttu-id="e3640-650"><xref:Microsoft.Extensions.Hosting.HostOptions> настраивает параметры для <xref:Microsoft.Extensions.Hosting.IHost>.</span><span class="sxs-lookup"><span data-stu-id="e3640-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="e3640-651">Время ожидания завершения работы</span><span class="sxs-lookup"><span data-stu-id="e3640-651">Shutdown timeout</span></span>

<span data-ttu-id="e3640-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> задает время ожидания для <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="e3640-653">Значение по умолчанию — пять секунд.</span><span class="sxs-lookup"><span data-stu-id="e3640-653">The default value is five seconds.</span></span>

<span data-ttu-id="e3640-654">Следующий пример конфигурации в `Program.Main` увеличивает значение времени ожидания завершения работы по умолчанию с 5 до 20 секунд.</span><span class="sxs-lookup"><span data-stu-id="e3640-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="e3640-655">Службы по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e3640-655">Default services</span></span>

<span data-ttu-id="e3640-656">Во время инициализации узла регистрируются следующие службы:</span><span class="sxs-lookup"><span data-stu-id="e3640-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="e3640-657">[Окружение](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="e3640-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="e3640-658">[Конфигурация](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="e3640-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="e3640-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e3640-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="e3640-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="e3640-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="e3640-661">[Параметры](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="e3640-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="e3640-662">[Ведение журнала](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="e3640-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="e3640-663">Конфигурация узла</span><span class="sxs-lookup"><span data-stu-id="e3640-663">Host configuration</span></span>

<span data-ttu-id="e3640-664">Существуют следующие подходы для создания конфигурации узла:</span><span class="sxs-lookup"><span data-stu-id="e3640-664">Host configuration is created by:</span></span>

* <span data-ttu-id="e3640-665">вызов методов расширения в <xref:Microsoft.Extensions.Hosting.IHostBuilder> для задания [корневой папки содержимого](#content-root) и [среды](#environment);</span><span class="sxs-lookup"><span data-stu-id="e3640-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="e3640-666">чтение конфигурации из поставщиков конфигурации в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="e3640-667">Методы расширения</span><span class="sxs-lookup"><span data-stu-id="e3640-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="e3640-668">Ключ приложения (имя)</span><span class="sxs-lookup"><span data-stu-id="e3640-668">Application key (name)</span></span>

<span data-ttu-id="e3640-669">Свойство [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) задается в конфигурации узла во время создания узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="e3640-670">Чтобы явно задать значение, используйте [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey).</span><span class="sxs-lookup"><span data-stu-id="e3640-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="e3640-671">**Ключ**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="e3640-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="e3640-672">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-672">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-673">**По умолчанию**: имя сборки, содержащей точку входа приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="e3640-674">**Задается с помощью**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="e3640-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="e3640-675">**Переменная среды**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e3640-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="e3640-676">Корневой каталог содержимого</span><span class="sxs-lookup"><span data-stu-id="e3640-676">Content root</span></span>

<span data-ttu-id="e3640-677">Этот параметр определяет, где узел начинает искать файлы содержимого.</span><span class="sxs-lookup"><span data-stu-id="e3640-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="e3640-678">**Ключ:** `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="e3640-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="e3640-679">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-679">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-680">**По умолчанию**: папка, в которой находится сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="e3640-681">**Задается с помощью**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="e3640-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="e3640-682">**Переменная среды**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e3640-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e3640-683">Если путь не существует, узел не запускается.</span><span class="sxs-lookup"><span data-stu-id="e3640-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="e3640-684">Дополнительные сведения можно найти в разделе [Корневой каталог содержимого](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="e3640-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="e3640-685">Среда</span><span class="sxs-lookup"><span data-stu-id="e3640-685">Environment</span></span>

<span data-ttu-id="e3640-686">Задает [среду](xref:fundamentals/environments) приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e3640-687">**Ключ:** `environment`</span><span class="sxs-lookup"><span data-stu-id="e3640-687">**Key**: `environment`</span></span>  
<span data-ttu-id="e3640-688">**Тип**: `string`</span><span class="sxs-lookup"><span data-stu-id="e3640-688">**Type**: `string`</span></span>  
<span data-ttu-id="e3640-689">**По умолчанию**: `Production`</span><span class="sxs-lookup"><span data-stu-id="e3640-689">**Default**: `Production`</span></span>  
<span data-ttu-id="e3640-690">**Задается с помощью**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="e3640-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="e3640-691">**Переменная среды**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>`[необязательно и определяется пользователем](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="e3640-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="e3640-692">В качестве среды можно указать любое значение.</span><span class="sxs-lookup"><span data-stu-id="e3640-692">The environment can be set to any value.</span></span> <span data-ttu-id="e3640-693">В платформе определены значения `Development`, `Staging` и `Production`.</span><span class="sxs-lookup"><span data-stu-id="e3640-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="e3640-694">Регистр символов в значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="e3640-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="e3640-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="e3640-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="e3640-696">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="e3640-697">Конфигурация узла применяется для инициализации <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> в целях использования в процессе сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="e3640-698">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e3640-699">Узел использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="e3640-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="e3640-700">Поставщики не включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3640-700">No providers are included by default.</span></span> <span data-ttu-id="e3640-701">Необходимо явно указать поставщики конфигурации, требуемые приложению в <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, в том числе:</span><span class="sxs-lookup"><span data-stu-id="e3640-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="e3640-702">конфигурация файла (например, из файла *hostsettings.json*);</span><span class="sxs-lookup"><span data-stu-id="e3640-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="e3640-703">конфигурация переменной среды;</span><span class="sxs-lookup"><span data-stu-id="e3640-703">Environment variable configuration.</span></span>
* <span data-ttu-id="e3640-704">конфигурация аргумента командной строки;</span><span class="sxs-lookup"><span data-stu-id="e3640-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="e3640-705">другие необходимые поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e3640-705">Any other required configuration providers.</span></span>

<span data-ttu-id="e3640-706">Конфигурация файла узла включается путем указания основного пути приложения с помощью `SetBasePath` и последующего вызова одного из [поставщиков конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="e3640-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="e3640-707">В примере приложения используется JSON-файл *hostsettings.json* и вызывается метод <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> для использования параметров конфигурации узла файла.</span><span class="sxs-lookup"><span data-stu-id="e3640-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="e3640-708">Чтобы добавить [конфигурацию переменной среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) узла, вызовите метод <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в построителе узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="e3640-709">`AddEnvironmentVariables` принимает необязательный определяемый пользователем префикс.</span><span class="sxs-lookup"><span data-stu-id="e3640-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="e3640-710">В примере приложения используется префикс `PREFIX_`.</span><span class="sxs-lookup"><span data-stu-id="e3640-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="e3640-711">Префикс удаляется при чтении переменных среды.</span><span class="sxs-lookup"><span data-stu-id="e3640-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="e3640-712">После настройки узла в примере приложения значение переменной среды для `PREFIX_ENVIRONMENT` становится значением конфигурации узла для ключа `environment`.</span><span class="sxs-lookup"><span data-stu-id="e3640-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="e3640-713">Во время разработки с использованием [Visual Studio](https://visualstudio.microsoft.com) или запуска приложения с помощью `dotnet run` переменные среды можно задать в файле *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e3640-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="e3640-714">В [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json* во время разработки.</span><span class="sxs-lookup"><span data-stu-id="e3640-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="e3640-715">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e3640-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e3640-716">[Конфигурация командной строки](xref:fundamentals/configuration/index#command-line-configuration-provider) добавляется путем вызова метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="e3640-717">Конфигурация командной строки добавляется в последнюю очередь, чтобы разрешить аргументам командной строки переопределять конфигурацию, предоставленную предыдущими поставщиками конфигурации.</span><span class="sxs-lookup"><span data-stu-id="e3640-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="e3640-718">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e3640-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="e3640-719">Дополнительную конфигурацию можно указать с помощью ключей [applicationName](#application-key-name) и [contentRoot](#content-root).</span><span class="sxs-lookup"><span data-stu-id="e3640-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="e3640-720">Пример конфигурации `HostBuilder` с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="e3640-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="e3640-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="e3640-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="e3640-722">Конфигурация приложения создается путем вызова метода <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> в реализации <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e3640-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="e3640-723">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> использует <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, чтобы создать экземпляр <xref:Microsoft.Extensions.Configuration.IConfiguration> для приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="e3640-724">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="e3640-725">Приложение использует значение, заданное последним для данного ключа.</span><span class="sxs-lookup"><span data-stu-id="e3640-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="e3640-726">Конфигурация, созданная с помощью <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, доступна в свойствах [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) для последующих операций и в <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="e3640-727">Конфигурация приложения автоматически получает конфигурацию узла, предоставленную с помощью [ConfigureHostConfiguration](#configurehostconfiguration).</span><span class="sxs-lookup"><span data-stu-id="e3640-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="e3640-728">Пример конфигурации приложения с использованием <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="e3640-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="e3640-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="e3640-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="e3640-730">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="e3640-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="e3640-731">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="e3640-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="e3640-732">Чтобы переместить файлы параметров в выходной каталог, укажите файлы параметров как [элементы проекта MSBuild](/visualstudio/msbuild/common-msbuild-project-items) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="e3640-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="e3640-733">Пример приложения перемещает свои файлы параметров приложения JSON и *hostsettings.json* со следующим элементом `<Content>`:</span><span class="sxs-lookup"><span data-stu-id="e3640-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="e3640-734">Для таких методов расширения конфигурации, как <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> и <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>, необходимы дополнительные пакеты NuGet, такие как [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) и [ Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span><span class="sxs-lookup"><span data-stu-id="e3640-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="e3640-735">Если приложение не использует [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), эти пакеты нужно добавить в проект в дополнение к основному пакету [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration).</span><span class="sxs-lookup"><span data-stu-id="e3640-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="e3640-736">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e3640-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="e3640-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="e3640-737">ConfigureServices</span></span>

<span data-ttu-id="e3640-738">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> добавляет службы в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e3640-739">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e3640-740">Размещенная служба — это класс с логикой фоновой задачи, реализующий интерфейс <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="e3640-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="e3640-741">Для получения дополнительной информации см. <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="e3640-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="e3640-742">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) использует метод расширения `AddHostedService` для добавления службы для событий времени жизни (`LifetimeEventsHostedService`) и синхронизированной фоновой задачи (`TimedHostedService`):</span><span class="sxs-lookup"><span data-stu-id="e3640-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="e3640-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="e3640-743">ConfigureLogging</span></span>

<span data-ttu-id="e3640-744">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> добавляет делегат для настройки указанного интерфейса <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e3640-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="e3640-745">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="e3640-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-746">UseConsoleLifetime</span></span>

<span data-ttu-id="e3640-747">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> прослушивает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>//SIGINT или SIGTERM и вызывает метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для запуска процесса завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="e3640-748">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> разблокирует расширения, такие как [RunAsync](#runasync) и [WaitForShutdownAsync](#waitforshutdownasync).</span><span class="sxs-lookup"><span data-stu-id="e3640-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="e3640-749">Класс `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` предварительно зарегистрирован и является реализацией времени жизни по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e3640-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="e3640-750">Используется то время жизни, которое зарегистрировано последним.</span><span class="sxs-lookup"><span data-stu-id="e3640-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="e3640-751">Конфигурация контейнера</span><span class="sxs-lookup"><span data-stu-id="e3640-751">Container configuration</span></span>

<span data-ttu-id="e3640-752">Для поддержки подключения к другим контейнерам узел может принимать <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="e3640-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="e3640-753">Предоставляет фабрику, не являющуюся частью регистрации контейнера внедрения зависимостей, а являющуюся встроенной функцией узла, которая используется для создания конкретных контейнеров внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="e3640-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="e3640-754">[UseServiceProviderFactory (IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) переопределяет фабрику по умолчанию, используемую для создания поставщика службы приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="e3640-755">Пользовательская конфигурация контейнера управляется методом <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="e3640-756">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> обеспечивает возможности строго типизированной настройки контейнера на основе базового API узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="e3640-757">Метод <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> может вызываться несколько раз с накоплением результатов.</span><span class="sxs-lookup"><span data-stu-id="e3640-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="e3640-758">Создание контейнера службы для приложения:</span><span class="sxs-lookup"><span data-stu-id="e3640-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="e3640-759">Настройка фабрики контейнера службы:</span><span class="sxs-lookup"><span data-stu-id="e3640-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="e3640-760">Использование фабрики и настройка пользовательского контейнера служб для приложения:</span><span class="sxs-lookup"><span data-stu-id="e3640-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="e3640-761">Расширение среды</span><span class="sxs-lookup"><span data-stu-id="e3640-761">Extensibility</span></span>

<span data-ttu-id="e3640-762">Расширяемость узла выполняется с помощью методов расширения класса <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span><span class="sxs-lookup"><span data-stu-id="e3640-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="e3640-763">В следующем примере показано, как метод расширения расширяет реализацию класса <xref:Microsoft.Extensions.Hosting.IHostBuilder> с помощью класса [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks), пример которого приведен в статье <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="e3640-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="e3640-764">Приложение устанавливает метод расширения `UseHostedService`, чтобы зарегистрировать размещенную службу, переданную в `T`:</span><span class="sxs-lookup"><span data-stu-id="e3640-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="e3640-765">Управление узлом</span><span class="sxs-lookup"><span data-stu-id="e3640-765">Manage the host</span></span>

<span data-ttu-id="e3640-766">Реализация <xref:Microsoft.Extensions.Hosting.IHost> отвечает за запуск и остановку реализаций <xref:Microsoft.Extensions.Hosting.IHostedService>, которые зарегистрированы в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="e3640-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="e3640-767">Выполнить</span><span class="sxs-lookup"><span data-stu-id="e3640-767">Run</span></span>

<span data-ttu-id="e3640-768">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> запускает приложение и блокирует вызывающий поток, пока работа узла не будет завершена:</span><span class="sxs-lookup"><span data-stu-id="e3640-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="e3640-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-769">RunAsync</span></span>

<span data-ttu-id="e3640-770">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> запускает приложение и возвращает объект <xref:System.Threading.Tasks.Task>, который завершается при активации токена отмены или завершение работы:</span><span class="sxs-lookup"><span data-stu-id="e3640-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="e3640-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-771">RunConsoleAsync</span></span>

<span data-ttu-id="e3640-772">Метод <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> включает поддержку консоли, собирает и запускает узел и ожидает сигналы <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM для завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="e3640-773">Start и StopAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-773">Start and StopAsync</span></span>

<span data-ttu-id="e3640-774">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> запускает узел синхронно.</span><span class="sxs-lookup"><span data-stu-id="e3640-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="e3640-775">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> пытается остановить узел в течение указанного периода ожидания.</span><span class="sxs-lookup"><span data-stu-id="e3640-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="e3640-776">StartAsync и StopAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="e3640-777">Метод <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="e3640-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="e3640-778">Метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> останавливает приложение.</span><span class="sxs-lookup"><span data-stu-id="e3640-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="e3640-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="e3640-779">WaitForShutdown</span></span>

<span data-ttu-id="e3640-780">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> запускается с помощью <xref:Microsoft.Extensions.Hosting.IHostLifetime>, например `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (прослушивает <kbd>CTRL</kbd>+<kbd>C</kbd>/SIGINT или SIGTERM).</span><span class="sxs-lookup"><span data-stu-id="e3640-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="e3640-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> вызывает <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="e3640-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="e3640-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="e3640-783">Метод <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> возвращает объект <xref:System.Threading.Tasks.Task>, который завершается после активации завершения работы через токен, и вызывает метод <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span><span class="sxs-lookup"><span data-stu-id="e3640-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="e3640-784">Внешнее управление</span><span class="sxs-lookup"><span data-stu-id="e3640-784">External control</span></span>

<span data-ttu-id="e3640-785">Внешнее управление узла может осуществляться с помощью методов, которые могут быть вызваны извне:</span><span class="sxs-lookup"><span data-stu-id="e3640-785">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="e3640-786">Метод <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> вызывается в начале <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, который ждет, пока он не будет завершен, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="e3640-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="e3640-787">Можно отложить запуск до получения сигнала от внешнего события.</span><span class="sxs-lookup"><span data-stu-id="e3640-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="e3640-788">Интерфейс IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="e3640-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="e3640-789">Интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> предоставляет сведения о среде размещения приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="e3640-790">Чтобы получить интерфейс <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> для использования его свойств и методов расширения, воспользуйтесь [внедрением конструктора](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="e3640-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="e3640-791">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="e3640-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="e3640-792">Интерфейс IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="e3640-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="e3640-793">Интерфейс <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> позволяет выполнять действия после запуска и завершения работы, включая запросы о нормальном завершении работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="e3640-794">Три свойства этого интерфейса представляют собой токены отмены, которые служат для регистрации методов <xref:System.Action>, определяющих события запуска и завершения работы.</span><span class="sxs-lookup"><span data-stu-id="e3640-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="e3640-795">Токен отмены</span><span class="sxs-lookup"><span data-stu-id="e3640-795">Cancellation Token</span></span> | <span data-ttu-id="e3640-796">Условие инициации&#8230;</span><span class="sxs-lookup"><span data-stu-id="e3640-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="e3640-797">Узел полностью запущен.</span><span class="sxs-lookup"><span data-stu-id="e3640-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="e3640-798">Заканчивается нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="e3640-799">Все запросы должны быть обработаны.</span><span class="sxs-lookup"><span data-stu-id="e3640-799">All requests should be processed.</span></span> <span data-ttu-id="e3640-800">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="e3640-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="e3640-801">Происходит нормальное завершение работы узла.</span><span class="sxs-lookup"><span data-stu-id="e3640-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="e3640-802">Запросы могут все еще обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="e3640-802">Requests may still be processing.</span></span> <span data-ttu-id="e3640-803">Завершение работы блокируется до тех пор, пока это событие не завершится.</span><span class="sxs-lookup"><span data-stu-id="e3640-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="e3640-804">Конструктор внедряет службу <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> в любой класс.</span><span class="sxs-lookup"><span data-stu-id="e3640-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="e3640-805">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) использует внедрение в конструкторе в класс `LifetimeEventsHostedService` (реализацию <xref:Microsoft.Extensions.Hosting.IHostedService>) для регистрации событий.</span><span class="sxs-lookup"><span data-stu-id="e3640-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="e3640-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="e3640-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="e3640-807">Метод <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> запрашивает остановку приложения.</span><span class="sxs-lookup"><span data-stu-id="e3640-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="e3640-808">Следующий класс использует <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> для корректного завершения работы приложения при вызове метода класса `Shutdown`:</span><span class="sxs-lookup"><span data-stu-id="e3640-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e3640-809">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e3640-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
