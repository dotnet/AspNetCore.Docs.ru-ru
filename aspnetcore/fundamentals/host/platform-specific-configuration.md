---
title: Использование начальных сборок размещения в ASP.NET Core
author: rick-anderson
description: Узнайте, как улучшить приложение ASP.NET Core из внешней сборки, используя реализацию IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: b39215a70f990afeb7d3fe0a62981113b154354e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588247"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="b578e-103">Использование начальных сборок размещения в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b578e-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="b578e-104">Автор: [Павел Крымец](https://github.com/pakrym) (Pavel Krymets)</span><span class="sxs-lookup"><span data-stu-id="b578e-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b578e-105">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="b578e-106">Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="b578e-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b578e-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b578e-108">Атрибут HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b578e-108">HostingStartup attribute</span></span>

<span data-ttu-id="b578e-109">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="b578e-110">Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-111">Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b578e-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="b578e-112">Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b578e-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="b578e-113">В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b578e-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b578e-114">Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b578e-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b578e-115">Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b578e-116">Обнаружение загруженных начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="b578e-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b578e-117">Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="b578e-118">В журнал вносятся ошибки, возникающие при загрузке сборок.</span><span class="sxs-lookup"><span data-stu-id="b578e-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="b578e-119">Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.</span><span class="sxs-lookup"><span data-stu-id="b578e-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b578e-120">Отключение автоматической загрузки начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="b578e-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="b578e-121">Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="b578e-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="b578e-122">Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="b578e-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="b578e-123">Параметр конфигурации узла "Запретить размещение при запуске".</span><span class="sxs-lookup"><span data-stu-id="b578e-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="b578e-124">Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="b578e-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="b578e-125">Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="b578e-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="b578e-126">Параметр конфигурации узла "Исключаемые сборки размещения при запуске".</span><span class="sxs-lookup"><span data-stu-id="b578e-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="b578e-127">Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="b578e-128">Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.</span><span class="sxs-lookup"><span data-stu-id="b578e-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="b578e-129">При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="b578e-130">Проект</span><span class="sxs-lookup"><span data-stu-id="b578e-130">Project</span></span>

<span data-ttu-id="b578e-131">Создайте размещение при запуске для любого из указанных ниже типов проектов:</span><span class="sxs-lookup"><span data-stu-id="b578e-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="b578e-132">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="b578e-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="b578e-133">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="b578e-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="b578e-134">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="b578e-134">Class library</span></span>

<span data-ttu-id="b578e-135">Расширение размещения при запуске можно указать в библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="b578e-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b578e-136">Библиотека содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b578e-137">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b578e-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="b578e-138">Библиотека классов:</span><span class="sxs-lookup"><span data-stu-id="b578e-138">The class library:</span></span>

* <span data-ttu-id="b578e-139">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b578e-140">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="b578e-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="b578e-141">Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="b578e-142">Метод `ServiceKeyInjection` класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="b578e-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b578e-144">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:</span><span class="sxs-lookup"><span data-stu-id="b578e-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="b578e-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b578e-146">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="b578e-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="b578e-147">Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее.</span><span class="sxs-lookup"><span data-stu-id="b578e-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="b578e-148">Пакет:</span><span class="sxs-lookup"><span data-stu-id="b578e-148">The package:</span></span>

* <span data-ttu-id="b578e-149">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b578e-150">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="b578e-151">Включает атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b578e-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b578e-153">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:</span><span class="sxs-lookup"><span data-stu-id="b578e-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="b578e-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b578e-155">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="b578e-155">Console app without an entry point</span></span>

<span data-ttu-id="b578e-156">*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="b578e-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="b578e-157">Расширение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть реализовано в консольном приложении без точки входа, содержащем атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-158">При публикации консольного приложения создается начальная сборка размещения, которая может использоваться в хранилище среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b578e-159">В этом процессе используется консольное приложение без точки входа, так как:</span><span class="sxs-lookup"><span data-stu-id="b578e-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b578e-160">Файл зависимостей необходим для функционирования размещения при запуске в начальной сборке размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="b578e-161">Файл зависимостей является ресурсом исполняемого приложения, который создается путем публикации приложения, а не библиотеки.</span><span class="sxs-lookup"><span data-stu-id="b578e-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="b578e-162">Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="b578e-163">В ходе создания динамического размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="b578e-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="b578e-164">Начальная сборка размещения создается из консольного приложения без точки входа, которое:</span><span class="sxs-lookup"><span data-stu-id="b578e-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="b578e-165">содержит класс с реализацией `IHostingStartup`;</span><span class="sxs-lookup"><span data-stu-id="b578e-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="b578e-166">содержит атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) для определения класса реализации `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="b578e-167">Консольное приложение публикуется для получения зависимостей начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="b578e-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="b578e-168">После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b578e-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="b578e-169">Файл зависимостей изменяется, чтобы задать расположение среды выполнения начальной сборки размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="b578e-170">Начальная сборка размещения и соответствующий файл зависимостей размещаются в хранилище пакетов среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="b578e-171">Чтобы можно было обнаружить начальную сборку размещения и ее файл зависимостей, они указываются в паре переменных среды.</span><span class="sxs-lookup"><span data-stu-id="b578e-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="b578e-172">Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="b578e-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="b578e-173">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="b578e-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="b578e-174">В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b578e-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b578e-175">Класс реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b578e-176">Метод класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="b578e-177">`IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b578e-178">При создании проекта `IHostingStartup` файл зависимостей ( *.deps.json*) задает расположение `runtime` для сборки в папке *bin*:</span><span class="sxs-lookup"><span data-stu-id="b578e-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b578e-179">Показана только часть файла.</span><span class="sxs-lookup"><span data-stu-id="b578e-179">Only part of the file is shown.</span></span> <span data-ttu-id="b578e-180">Имя сборки в примере — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b578e-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b578e-181">Конфигурация, предоставляемая размещением при запуске</span><span class="sxs-lookup"><span data-stu-id="b578e-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="b578e-182">Существует два подхода к подготовке конфигурации в зависимости от того, какой конфигурацией вы хотите отдать приоритет — конфигурации размещения при запуске или конфигурации приложения:</span><span class="sxs-lookup"><span data-stu-id="b578e-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="b578e-183">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> для загрузки конфигурации после выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b578e-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b578e-184">При таком подходе конфигурация размещения при запуске будет иметь приоритет над конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="b578e-185">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> для загрузки конфигурации до выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b578e-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b578e-186">При таком подходе конфигурация приложения будет иметь приоритет над конфигурацией размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b578e-187">Указание начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="b578e-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="b578e-188">Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b578e-189">Переменная среды — это список сборок, разделенный точками с запятой.</span><span class="sxs-lookup"><span data-stu-id="b578e-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="b578e-190">Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-191">Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:</span><span class="sxs-lookup"><span data-stu-id="b578e-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b578e-192">Начальную сборку размещения также можно задать с помощью параметра конфигурации узла "Начальные сборки размещения".</span><span class="sxs-lookup"><span data-stu-id="b578e-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="b578e-193">При наличии нескольких начальных сборок размещения их методы <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> выполняются в порядке расположения сборок.</span><span class="sxs-lookup"><span data-stu-id="b578e-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="b578e-194">Активация</span><span class="sxs-lookup"><span data-stu-id="b578e-194">Activation</span></span>

<span data-ttu-id="b578e-195">Ниже приведены варианты активации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="b578e-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="b578e-196">[Хранилище среды выполнения.](#runtime-store) Для активации не требуется ссылка во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b578e-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="b578e-197">Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами.</span><span class="sxs-lookup"><span data-stu-id="b578e-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="b578e-198">Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="b578e-199">Ссылки во время компиляции, необходимые для активации</span><span class="sxs-lookup"><span data-stu-id="b578e-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="b578e-200">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="b578e-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="b578e-201">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="b578e-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="b578e-202">Хранилище среды выполнения</span><span class="sxs-lookup"><span data-stu-id="b578e-202">Runtime store</span></span>

<span data-ttu-id="b578e-203">Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="b578e-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="b578e-204">Ссылка на сборку во время компиляции не требуется расширенному приложению.</span><span class="sxs-lookup"><span data-stu-id="b578e-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b578e-205">После начальной сборки размещения создается хранилище среды выполнения с помощью файла манифеста проекта и команды [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="b578e-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b578e-206">В примере приложения (проект *RuntimeStore*) используется такая команда:</span><span class="sxs-lookup"><span data-stu-id="b578e-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b578e-207">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение хранилища среды выполнения добавляется к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="b578e-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="b578e-208">**Изменение и размещение файла зависимостей размещения при запуске**</span><span class="sxs-lookup"><span data-stu-id="b578e-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b578e-209">Чтобы активировать расширение без ссылки на пакет расширения, укажите дополнительные зависимости в среде выполнения с помощью `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="b578e-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="b578e-210">`additionalDeps` предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="b578e-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="b578e-211">Расширять граф библиотеки приложения, предоставляя набор дополнительных файлов *.deps.json* для объединения с собственным файлом *.deps.json* приложения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="b578e-212">Обнаруживать и скачивать начальную сборку размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="b578e-213">Рекомендуемые действия при создании файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b578e-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="b578e-214">Выполните команду `dotnet publish` для файла манифеста хранилища среды выполнения, ссылка на который создавалась в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="b578e-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="b578e-215">Удалите ссылку на манифест из библиотек и раздела `runtime` итогового файла *deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b578e-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="b578e-216">В примере проекта свойство `store.manifest/1.0.0` удаляется из разделов `targets` и `libraries`.</span><span class="sxs-lookup"><span data-stu-id="b578e-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="b578e-217">Поместите файл *.deps.json* в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="b578e-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b578e-218">`{ADDITIONAL DEPENDENCIES PATH}`. Расположение, добавляемое к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b578e-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="b578e-219">`{SHARED FRAMEWORK NAME}`. Общая платформа, требуемая для этого файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b578e-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="b578e-220">`{SHARED FRAMEWORK VERSION}`. Минимальная версия общей платформы.</span><span class="sxs-lookup"><span data-stu-id="b578e-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="b578e-221">`{ENHANCEMENT ASSEMBLY NAME}`. Имя сборки расширения.</span><span class="sxs-lookup"><span data-stu-id="b578e-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="b578e-222">В примере приложения (проект *RuntimeStore*) файл дополнительных зависимостей помещается в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="b578e-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b578e-223">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение файла дополнительных зависимостей добавляется к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b578e-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="b578e-224">В примере приложения (проект *RuntimeStore*) для сборки хранилища среды выполнения и создания файла дополнительных зависимостей используется скрипт [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="b578e-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="b578e-225">Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b578e-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b578e-226">**Развертывание**</span><span class="sxs-lookup"><span data-stu-id="b578e-226">**Deployment**</span></span>

<span data-ttu-id="b578e-227">Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:</span><span class="sxs-lookup"><span data-stu-id="b578e-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="b578e-228">Хранилище среды выполнения размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="b578e-229">Файл зависимостей размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="b578e-230">Скрипт PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b578e-231">Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.</span><span class="sxs-lookup"><span data-stu-id="b578e-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b578e-232">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="b578e-232">NuGet package</span></span>

<span data-ttu-id="b578e-233">Расширение размещения при запуске можно указать в пакете NuGet.</span><span class="sxs-lookup"><span data-stu-id="b578e-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b578e-234">Пакет содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-235">Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="b578e-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="b578e-236">Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="b578e-237">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="b578e-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b578e-238">Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="b578e-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b578e-239">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="b578e-240">Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:</span><span class="sxs-lookup"><span data-stu-id="b578e-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="b578e-241">Создание пакета NuGet с помощью кроссплатформенных средств</span><span class="sxs-lookup"><span data-stu-id="b578e-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="b578e-242">Публикация пакетов</span><span class="sxs-lookup"><span data-stu-id="b578e-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="b578e-243">Хранилище пакетов среды выполнения</span><span class="sxs-lookup"><span data-stu-id="b578e-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="b578e-244">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="b578e-244">Project bin folder</span></span>

<span data-ttu-id="b578e-245">Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="b578e-246">Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="b578e-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="b578e-247">Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="b578e-248">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="b578e-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b578e-249">Этот подход применяется при вызове сценария развертывания для создания ссылки времени компиляции на сборку запуска размещения (*DLL*-файл) и перемещения сборки в одно из следующих мест:</span><span class="sxs-lookup"><span data-stu-id="b578e-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="b578e-250">Проект, который будет использовать ее.</span><span class="sxs-lookup"><span data-stu-id="b578e-250">The consuming project.</span></span>
  * <span data-ttu-id="b578e-251">Расположение, доступное использующему проекту.</span><span class="sxs-lookup"><span data-stu-id="b578e-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="b578e-252">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="b578e-253">При нацеливании на .NET Framework сборка загружается в контексте загрузки по умолчанию, что для .NET Framework означает, что сборка находится в одном из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="b578e-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="b578e-254">Базовый путь приложения. Папка *bin*, в которой находится исполняемый файл приложения ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="b578e-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="b578e-255">Глобальный кэш сборок. В глобальном кэше сборок сохраняются сборки, которые могут использоваться несколькими приложениями .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b578e-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="b578e-256">Дополнительные сведения см. в разделе [Практическое руководство. Установка сборки в глобальный кэш сборок](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) в документации по .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b578e-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="b578e-257">Пример кода</span><span class="sxs-lookup"><span data-stu-id="b578e-257">Sample code</span></span>

<span data-ttu-id="b578e-258">В [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="b578e-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="b578e-259">В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:</span><span class="sxs-lookup"><span data-stu-id="b578e-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b578e-260">Пакет NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b578e-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b578e-261">Библиотека классов (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b578e-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="b578e-262">Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="b578e-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="b578e-263">Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:</span><span class="sxs-lookup"><span data-stu-id="b578e-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b578e-264">Зарегистрированные службы</span><span class="sxs-lookup"><span data-stu-id="b578e-264">Registered services</span></span>
  * <span data-ttu-id="b578e-265">Адрес (схема, узел, базовый путь, путь, строка запроса)</span><span class="sxs-lookup"><span data-stu-id="b578e-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="b578e-266">Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)</span><span class="sxs-lookup"><span data-stu-id="b578e-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="b578e-267">Заголовки запросов</span><span class="sxs-lookup"><span data-stu-id="b578e-267">Request headers</span></span>
  * <span data-ttu-id="b578e-268">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="b578e-268">Environment variables</span></span>

<span data-ttu-id="b578e-269">Для выполнения образца:</span><span class="sxs-lookup"><span data-stu-id="b578e-269">To run the sample:</span></span>

<span data-ttu-id="b578e-270">**Активация из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="b578e-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="b578e-271">Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="b578e-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="b578e-272">Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b578e-273">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-273">Compile and run the app.</span></span> <span data-ttu-id="b578e-274">Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b578e-275">Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета ( *../ HostingStartupPackage/bin/Debug*) в качестве источника пакета.</span><span class="sxs-lookup"><span data-stu-id="b578e-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="b578e-276">Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b578e-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b578e-277">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b578e-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b578e-278">Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша.</span><span class="sxs-lookup"><span data-stu-id="b578e-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="b578e-279">Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="b578e-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="b578e-280">**Активации из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="b578e-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="b578e-281">Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="b578e-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="b578e-282">Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b578e-283">Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="b578e-284">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-284">Compile and run the app.</span></span> <span data-ttu-id="b578e-285">`<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b578e-286">Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b578e-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b578e-287">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b578e-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b578e-288">**Активация из сборки среды выполнения, развернутой в хранилище**</span><span class="sxs-lookup"><span data-stu-id="b578e-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="b578e-289">В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b578e-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="b578e-290">PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1).</span><span class="sxs-lookup"><span data-stu-id="b578e-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="b578e-291">Для установки PowerShell на других платформах см. раздел [Установка разных версий PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="b578e-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="b578e-292">Выполните сценарий *build.ps1*, находящийся в папке *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="b578e-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b578e-293">Скрипт выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="b578e-293">The script:</span></span>
   * <span data-ttu-id="b578e-294">Генерирует пакет `StartupDiagnostics` в папке *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="b578e-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="b578e-295">Создает хранилище среды выполнения для `StartupDiagnostics` в папке *store*.</span><span class="sxs-lookup"><span data-stu-id="b578e-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="b578e-296">Для размещения при запуске, развернутом в Windows, команда `dotnet store` в сценарии использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="b578e-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b578e-297">При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID в строке 37 скрипта.</span><span class="sxs-lookup"><span data-stu-id="b578e-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="b578e-298">Хранилище среды выполнения для `StartupDiagnostics` позже будет перемещено в хранилище среды выполнения пользователя или системы на компьютере, где будет израсходована сборка.</span><span class="sxs-lookup"><span data-stu-id="b578e-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="b578e-299">Пользовательское место установки хранилища среды выполнения для сборки `StartupDiagnostics` — *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="b578e-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="b578e-300">Генерирует `additionalDeps` для `StartupDiagnostics` в папке *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="b578e-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="b578e-301">Дополнительные зависимости позднее будут перенесены в дополнительные зависимости пользователя или системы.</span><span class="sxs-lookup"><span data-stu-id="b578e-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="b578e-302">Расположением установки дополнительных зависимостей пользователя `StartupDiagnostics` является *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b578e-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="b578e-303">Размещает файл *deploy.ps1* в папке для *развертывания*.</span><span class="sxs-lookup"><span data-stu-id="b578e-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="b578e-304">Выполните сценарий *deploy.ps1*, находящийся в папке *deployment*.</span><span class="sxs-lookup"><span data-stu-id="b578e-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="b578e-305">Скрипт присоединяет:</span><span class="sxs-lookup"><span data-stu-id="b578e-305">The script appends:</span></span>
   * <span data-ttu-id="b578e-306">`StartupDiagnostics` к переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="b578e-307">Путь размещения начальных зависимостей (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b578e-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="b578e-308">Путь хранилища среды выполнения (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="b578e-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="b578e-309">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-309">Run the sample app.</span></span>
1. <span data-ttu-id="b578e-310">Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="b578e-311">Запросите конечную точку `/diag` для просмотра диагностических сведений.</span><span class="sxs-lookup"><span data-stu-id="b578e-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b578e-312">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="b578e-313">Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="b578e-314">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b578e-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="b578e-315">Атрибут HostingStartup</span><span class="sxs-lookup"><span data-stu-id="b578e-315">HostingStartup attribute</span></span>

<span data-ttu-id="b578e-316">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="b578e-317">Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-318">Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b578e-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="b578e-319">Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="b578e-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="b578e-320">Дополнительные сведения см. в руководстве по [настройке Размещение начальных сборок](xref:fundamentals/host/web-host#hosting-startup-assemblies) и [Веб-узел: исключаемые начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b578e-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="b578e-321">В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b578e-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="b578e-322">Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b578e-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b578e-323">Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="b578e-324">Обнаружение загруженных начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="b578e-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="b578e-325">Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="b578e-326">В журнал вносятся ошибки, возникающие при загрузке сборок.</span><span class="sxs-lookup"><span data-stu-id="b578e-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="b578e-327">Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.</span><span class="sxs-lookup"><span data-stu-id="b578e-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="b578e-328">Отключение автоматической загрузки начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="b578e-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="b578e-329">Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="b578e-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="b578e-330">Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="b578e-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="b578e-331">Параметр конфигурации узла [Запретить размещение при запуске](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="b578e-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="b578e-332">Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="b578e-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="b578e-333">Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="b578e-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="b578e-334">Параметр конфигурации узла [Исключаемые сборки размещения при запуске](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b578e-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="b578e-335">Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="b578e-336">Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.</span><span class="sxs-lookup"><span data-stu-id="b578e-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="b578e-337">При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="b578e-338">Проект</span><span class="sxs-lookup"><span data-stu-id="b578e-338">Project</span></span>

<span data-ttu-id="b578e-339">Создайте размещение при запуске для любого из указанных ниже типов проектов:</span><span class="sxs-lookup"><span data-stu-id="b578e-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="b578e-340">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="b578e-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="b578e-341">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="b578e-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="b578e-342">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="b578e-342">Class library</span></span>

<span data-ttu-id="b578e-343">Расширение размещения при запуске можно указать в библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="b578e-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="b578e-344">Библиотека содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b578e-345">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="b578e-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="b578e-346">Библиотека классов:</span><span class="sxs-lookup"><span data-stu-id="b578e-346">The class library:</span></span>

* <span data-ttu-id="b578e-347">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b578e-348">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="b578e-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="b578e-349">Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="b578e-350">Метод `ServiceKeyInjection` класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="b578e-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b578e-352">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:</span><span class="sxs-lookup"><span data-stu-id="b578e-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="b578e-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="b578e-354">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="b578e-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="b578e-355">Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее.</span><span class="sxs-lookup"><span data-stu-id="b578e-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="b578e-356">Пакет:</span><span class="sxs-lookup"><span data-stu-id="b578e-356">The package:</span></span>

* <span data-ttu-id="b578e-357">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="b578e-358">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="b578e-359">Включает атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="b578e-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="b578e-361">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:</span><span class="sxs-lookup"><span data-stu-id="b578e-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="b578e-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b578e-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="b578e-363">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="b578e-363">Console app without an entry point</span></span>

<span data-ttu-id="b578e-364">*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="b578e-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="b578e-365">Расширение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть реализовано в консольном приложении без точки входа, содержащем атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-366">При публикации консольного приложения создается начальная сборка размещения, которая может использоваться в хранилище среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="b578e-367">В этом процессе используется консольное приложение без точки входа, так как:</span><span class="sxs-lookup"><span data-stu-id="b578e-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="b578e-368">Файл зависимостей необходим для функционирования размещения при запуске в начальной сборке размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="b578e-369">Файл зависимостей является ресурсом исполняемого приложения, который создается путем публикации приложения, а не библиотеки.</span><span class="sxs-lookup"><span data-stu-id="b578e-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="b578e-370">Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="b578e-371">В ходе создания динамического размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="b578e-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="b578e-372">Начальная сборка размещения создается из консольного приложения без точки входа, которое:</span><span class="sxs-lookup"><span data-stu-id="b578e-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="b578e-373">содержит класс с реализацией `IHostingStartup`;</span><span class="sxs-lookup"><span data-stu-id="b578e-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="b578e-374">содержит атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) для определения класса реализации `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="b578e-375">Консольное приложение публикуется для получения зависимостей начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="b578e-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="b578e-376">После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b578e-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="b578e-377">Файл зависимостей изменяется, чтобы задать расположение среды выполнения начальной сборки размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="b578e-378">Начальная сборка размещения и соответствующий файл зависимостей размещаются в хранилище пакетов среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b578e-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="b578e-379">Чтобы можно было обнаружить начальную сборку размещения и ее файл зависимостей, они указываются в паре переменных среды.</span><span class="sxs-lookup"><span data-stu-id="b578e-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="b578e-380">Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="b578e-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="b578e-381">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="b578e-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="b578e-382">В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="b578e-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="b578e-383">Класс реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="b578e-384">Метод класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="b578e-385">`IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="b578e-386">При создании проекта `IHostingStartup` файл зависимостей ( *.deps.json*) задает расположение `runtime` для сборки в папке *bin*:</span><span class="sxs-lookup"><span data-stu-id="b578e-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="b578e-387">Показана только часть файла.</span><span class="sxs-lookup"><span data-stu-id="b578e-387">Only part of the file is shown.</span></span> <span data-ttu-id="b578e-388">Имя сборки в примере — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="b578e-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="b578e-389">Конфигурация, предоставляемая размещением при запуске</span><span class="sxs-lookup"><span data-stu-id="b578e-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="b578e-390">Существует два подхода к подготовке конфигурации в зависимости от того, какой конфигурацией вы хотите отдать приоритет — конфигурации размещения при запуске или конфигурации приложения:</span><span class="sxs-lookup"><span data-stu-id="b578e-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="b578e-391">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> для загрузки конфигурации после выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b578e-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b578e-392">При таком подходе конфигурация размещения при запуске будет иметь приоритет над конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="b578e-393">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> для загрузки конфигурации до выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="b578e-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="b578e-394">При таком подходе конфигурация приложения будет иметь приоритет над конфигурацией размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="b578e-395">Указание начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="b578e-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="b578e-396">Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="b578e-397">Переменная среды — это список сборок, разделенный точками с запятой.</span><span class="sxs-lookup"><span data-stu-id="b578e-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="b578e-398">Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-399">Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:</span><span class="sxs-lookup"><span data-stu-id="b578e-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="b578e-400">Начальную сборку размещения также можно задать с помощью параметра конфигурации узла [Начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="b578e-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="b578e-401">При наличии нескольких начальных сборок размещения их методы <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> выполняются в порядке расположения сборок.</span><span class="sxs-lookup"><span data-stu-id="b578e-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="b578e-402">Активация</span><span class="sxs-lookup"><span data-stu-id="b578e-402">Activation</span></span>

<span data-ttu-id="b578e-403">Ниже приведены варианты активации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="b578e-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="b578e-404">[Хранилище среды выполнения.](#runtime-store) Для активации не требуется ссылка во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="b578e-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="b578e-405">Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами.</span><span class="sxs-lookup"><span data-stu-id="b578e-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="b578e-406">Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="b578e-407">Ссылки во время компиляции, необходимые для активации</span><span class="sxs-lookup"><span data-stu-id="b578e-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="b578e-408">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="b578e-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="b578e-409">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="b578e-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="b578e-410">Хранилище среды выполнения</span><span class="sxs-lookup"><span data-stu-id="b578e-410">Runtime store</span></span>

<span data-ttu-id="b578e-411">Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="b578e-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="b578e-412">Ссылка на сборку во время компиляции не требуется расширенному приложению.</span><span class="sxs-lookup"><span data-stu-id="b578e-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="b578e-413">После начальной сборки размещения создается хранилище среды выполнения с помощью файла манифеста проекта и команды [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="b578e-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="b578e-414">В примере приложения (проект *RuntimeStore*) используется такая команда:</span><span class="sxs-lookup"><span data-stu-id="b578e-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="b578e-415">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение хранилища среды выполнения добавляется к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="b578e-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="b578e-416">**Изменение и размещение файла зависимостей размещения при запуске**</span><span class="sxs-lookup"><span data-stu-id="b578e-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="b578e-417">Чтобы активировать расширение без ссылки на пакет расширения, укажите дополнительные зависимости в среде выполнения с помощью `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="b578e-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="b578e-418">`additionalDeps` предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="b578e-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="b578e-419">Расширять граф библиотеки приложения, предоставляя набор дополнительных файлов *.deps.json* для объединения с собственным файлом *.deps.json* приложения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="b578e-420">Обнаруживать и скачивать начальную сборку размещения.</span><span class="sxs-lookup"><span data-stu-id="b578e-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="b578e-421">Рекомендуемые действия при создании файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b578e-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="b578e-422">Выполните команду `dotnet publish` для файла манифеста хранилища среды выполнения, ссылка на который создавалась в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="b578e-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="b578e-423">Удалите ссылку на манифест из библиотек и раздела `runtime` итогового файла *deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b578e-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="b578e-424">В примере проекта свойство `store.manifest/1.0.0` удаляется из разделов `targets` и `libraries`.</span><span class="sxs-lookup"><span data-stu-id="b578e-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="b578e-425">Поместите файл *.deps.json* в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="b578e-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="b578e-426">`{ADDITIONAL DEPENDENCIES PATH}`. Расположение, добавляемое к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b578e-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="b578e-427">`{SHARED FRAMEWORK NAME}`. Общая платформа, требуемая для этого файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="b578e-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="b578e-428">`{SHARED FRAMEWORK VERSION}`. Минимальная версия общей платформы.</span><span class="sxs-lookup"><span data-stu-id="b578e-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="b578e-429">`{ENHANCEMENT ASSEMBLY NAME}`. Имя сборки расширения.</span><span class="sxs-lookup"><span data-stu-id="b578e-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="b578e-430">В примере приложения (проект *RuntimeStore*) файл дополнительных зависимостей помещается в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="b578e-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="b578e-431">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение файла дополнительных зависимостей добавляется к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b578e-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="b578e-432">В примере приложения (проект *RuntimeStore*) для сборки хранилища среды выполнения и создания файла дополнительных зависимостей используется скрипт [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="b578e-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="b578e-433">Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b578e-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b578e-434">**Развертывание**</span><span class="sxs-lookup"><span data-stu-id="b578e-434">**Deployment**</span></span>

<span data-ttu-id="b578e-435">Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:</span><span class="sxs-lookup"><span data-stu-id="b578e-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="b578e-436">Хранилище среды выполнения размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="b578e-437">Файл зависимостей размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="b578e-438">Скрипт PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="b578e-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="b578e-439">Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.</span><span class="sxs-lookup"><span data-stu-id="b578e-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="b578e-440">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="b578e-440">NuGet package</span></span>

<span data-ttu-id="b578e-441">Расширение размещения при запуске можно указать в пакете NuGet.</span><span class="sxs-lookup"><span data-stu-id="b578e-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="b578e-442">Пакет содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="b578e-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="b578e-443">Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="b578e-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="b578e-444">Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="b578e-445">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="b578e-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b578e-446">Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="b578e-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="b578e-447">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="b578e-448">Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:</span><span class="sxs-lookup"><span data-stu-id="b578e-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="b578e-449">Создание пакета NuGet с помощью кроссплатформенных средств</span><span class="sxs-lookup"><span data-stu-id="b578e-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="b578e-450">Публикация пакетов</span><span class="sxs-lookup"><span data-stu-id="b578e-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="b578e-451">Хранилище пакетов среды выполнения</span><span class="sxs-lookup"><span data-stu-id="b578e-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="b578e-452">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="b578e-452">Project bin folder</span></span>

<span data-ttu-id="b578e-453">Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="b578e-454">Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="b578e-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="b578e-455">Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="b578e-456">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="b578e-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="b578e-457">Этот подход применяется при вызове сценария развертывания для создания ссылки времени компиляции на сборку запуска размещения (*DLL*-файл) и перемещения сборки в одно из следующих мест:</span><span class="sxs-lookup"><span data-stu-id="b578e-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="b578e-458">Проект, который будет использовать ее.</span><span class="sxs-lookup"><span data-stu-id="b578e-458">The consuming project.</span></span>
  * <span data-ttu-id="b578e-459">Расположение, доступное использующему проекту.</span><span class="sxs-lookup"><span data-stu-id="b578e-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="b578e-460">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="b578e-461">При нацеливании на .NET Framework сборка загружается в контексте загрузки по умолчанию, что для .NET Framework означает, что сборка находится в одном из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="b578e-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="b578e-462">Базовый путь приложения. Папка *bin*, в которой находится исполняемый файл приложения ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="b578e-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="b578e-463">Глобальный кэш сборок. В глобальном кэше сборок сохраняются сборки, которые могут использоваться несколькими приложениями .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b578e-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="b578e-464">Дополнительные сведения см. в разделе [Практическое руководство. Установка сборки в глобальный кэш сборок](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) в документации по .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b578e-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="b578e-465">Пример кода</span><span class="sxs-lookup"><span data-stu-id="b578e-465">Sample code</span></span>

<span data-ttu-id="b578e-466">В [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="b578e-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="b578e-467">В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:</span><span class="sxs-lookup"><span data-stu-id="b578e-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="b578e-468">Пакет NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="b578e-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="b578e-469">Библиотека классов (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="b578e-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="b578e-470">Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="b578e-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="b578e-471">Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:</span><span class="sxs-lookup"><span data-stu-id="b578e-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="b578e-472">Зарегистрированные службы</span><span class="sxs-lookup"><span data-stu-id="b578e-472">Registered services</span></span>
  * <span data-ttu-id="b578e-473">Адрес (схема, узел, базовый путь, путь, строка запроса)</span><span class="sxs-lookup"><span data-stu-id="b578e-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="b578e-474">Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)</span><span class="sxs-lookup"><span data-stu-id="b578e-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="b578e-475">Заголовки запросов</span><span class="sxs-lookup"><span data-stu-id="b578e-475">Request headers</span></span>
  * <span data-ttu-id="b578e-476">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="b578e-476">Environment variables</span></span>

<span data-ttu-id="b578e-477">Для выполнения образца:</span><span class="sxs-lookup"><span data-stu-id="b578e-477">To run the sample:</span></span>

<span data-ttu-id="b578e-478">**Активация из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="b578e-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="b578e-479">Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="b578e-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="b578e-480">Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b578e-481">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-481">Compile and run the app.</span></span> <span data-ttu-id="b578e-482">Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="b578e-483">Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета ( *../ HostingStartupPackage/bin/Debug*) в качестве источника пакета.</span><span class="sxs-lookup"><span data-stu-id="b578e-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="b578e-484">Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b578e-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="b578e-485">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b578e-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b578e-486">Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша.</span><span class="sxs-lookup"><span data-stu-id="b578e-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="b578e-487">Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="b578e-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="b578e-488">**Активации из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="b578e-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="b578e-489">Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="b578e-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="b578e-490">Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="b578e-491">Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="b578e-492">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="b578e-492">Compile and run the app.</span></span> <span data-ttu-id="b578e-493">`<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="b578e-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="b578e-494">Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="b578e-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="b578e-495">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b578e-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="b578e-496">**Активация из сборки среды выполнения, развернутой в хранилище**</span><span class="sxs-lookup"><span data-stu-id="b578e-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="b578e-497">В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b578e-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="b578e-498">PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1).</span><span class="sxs-lookup"><span data-stu-id="b578e-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="b578e-499">Для установки PowerShell на других платформах см. раздел [Установка разных версий PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="b578e-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="b578e-500">Выполните сценарий *build.ps1*, находящийся в папке *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="b578e-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="b578e-501">Скрипт выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="b578e-501">The script:</span></span>
   * <span data-ttu-id="b578e-502">Генерирует пакет `StartupDiagnostics` в папке *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="b578e-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="b578e-503">Создает хранилище среды выполнения для `StartupDiagnostics` в папке *store*.</span><span class="sxs-lookup"><span data-stu-id="b578e-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="b578e-504">Для размещения при запуске, развернутом в Windows, команда `dotnet store` в сценарии использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="b578e-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="b578e-505">При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID в строке 37 скрипта.</span><span class="sxs-lookup"><span data-stu-id="b578e-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="b578e-506">Хранилище среды выполнения для `StartupDiagnostics` позже будет перемещено в хранилище среды выполнения пользователя или системы на компьютере, где будет израсходована сборка.</span><span class="sxs-lookup"><span data-stu-id="b578e-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="b578e-507">Пользовательское место установки хранилища среды выполнения для сборки `StartupDiagnostics` — *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="b578e-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="b578e-508">Генерирует `additionalDeps` для `StartupDiagnostics` в папке *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="b578e-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="b578e-509">Дополнительные зависимости позднее будут перенесены в дополнительные зависимости пользователя или системы.</span><span class="sxs-lookup"><span data-stu-id="b578e-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="b578e-510">Расположением установки дополнительных зависимостей пользователя `StartupDiagnostics` является *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="b578e-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="b578e-511">Размещает файл *deploy.ps1* в папке для *развертывания*.</span><span class="sxs-lookup"><span data-stu-id="b578e-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="b578e-512">Выполните сценарий *deploy.ps1*, находящийся в папке *deployment*.</span><span class="sxs-lookup"><span data-stu-id="b578e-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="b578e-513">Скрипт присоединяет:</span><span class="sxs-lookup"><span data-stu-id="b578e-513">The script appends:</span></span>
   * <span data-ttu-id="b578e-514">`StartupDiagnostics` к переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="b578e-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="b578e-515">Путь размещения начальных зависимостей (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="b578e-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="b578e-516">Путь хранилища среды выполнения (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="b578e-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="b578e-517">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-517">Run the sample app.</span></span>
1. <span data-ttu-id="b578e-518">Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения.</span><span class="sxs-lookup"><span data-stu-id="b578e-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="b578e-519">Запросите конечную точку `/diag` для просмотра диагностических сведений.</span><span class="sxs-lookup"><span data-stu-id="b578e-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
