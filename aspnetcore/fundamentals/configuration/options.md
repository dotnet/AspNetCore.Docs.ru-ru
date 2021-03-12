---
title: Шаблон параметров в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать шаблон параметров для представления групп связанных параметров в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: cb78147050ebdafc7de4ad150ae2644689d6efbc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586219"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="68557-103">Шаблон параметров в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68557-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68557-104">Авторы: [Кирк Ларкин (Kirk Larkin)](https://twitter.com/serpent5) и [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="68557-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="68557-105">Шаблон параметров использует классы для обеспечения строго типизированного доступа к группам связанных параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="68557-106">Когда [параметры конфигурации](xref:fundamentals/configuration/index) изолируются по сценарию в отдельных классах, в приложениях соблюдаются два важных принципа программной инженерии.</span><span class="sxs-lookup"><span data-stu-id="68557-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="68557-107">[Принцип разделения интерфейса (ISP) или инкапсуляция.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) Сценарии (классы), которые зависят от параметров конфигурации, зависят только от используемых ими параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="68557-108">[Разделение областей ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Параметры для разных частей приложения не зависят друг от друга и не связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="68557-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="68557-109">В параметрах также предусмотрен механизм для проверки данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="68557-110">Дополнительные сведения см. в разделе [Проверка параметров](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="68557-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="68557-111">В этом разделе приводятся сведения о шаблоне параметров в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68557-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="68557-112">Сведения об использовании шаблона параметров в консольных приложениях см. в разделе [Шаблон параметров в .NET](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="68557-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="68557-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/options/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68557-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="68557-114">Привязка иерархической конфигурации</span><span class="sxs-lookup"><span data-stu-id="68557-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="68557-115">Интерфейсы параметров</span><span class="sxs-lookup"><span data-stu-id="68557-115">Options interfaces</span></span>

<span data-ttu-id="68557-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="68557-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="68557-117">***Не*** поддерживает:</span><span class="sxs-lookup"><span data-stu-id="68557-117">Does ***not*** support:</span></span>
  * <span data-ttu-id="68557-118">чтение данных конфигурации после запуска приложения;</span><span class="sxs-lookup"><span data-stu-id="68557-118">Reading of configuration data after the app has started.</span></span>
  * <span data-ttu-id="68557-119">[именованные параметры](#named);</span><span class="sxs-lookup"><span data-stu-id="68557-119">[Named options](#named)</span></span>
* <span data-ttu-id="68557-120">Регистрируется в качестве элемента [singleton](xref:fundamentals/dependency-injection#singleton) и может быть внедрен в любое [время существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="68557-120">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="68557-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="68557-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="68557-122">Полезно использовать в сценариях, когда параметры нужно заново вычислять при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="68557-122">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="68557-123">Дополнительные сведения см. в разделе [Использование IOptionsSnapshot для чтения обновленных данных](#ios).</span><span class="sxs-lookup"><span data-stu-id="68557-123">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="68557-124">Регистрируется как [Scoped](xref:fundamentals/dependency-injection#scoped) (с областью), поэтому его нельзя внедрить в службу singleton.</span><span class="sxs-lookup"><span data-stu-id="68557-124">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="68557-125">Поддерживает [именованные параметры](#named).</span><span class="sxs-lookup"><span data-stu-id="68557-125">Supports [named options](#named)</span></span>

<span data-ttu-id="68557-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="68557-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="68557-127">Используется для извлечения параметров и управления уведомлениями о параметрах для экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-127">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="68557-128">Регистрируется в качестве элемента [singleton](xref:fundamentals/dependency-injection#singleton) и может быть внедрен в любое [время существования службы](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="68557-128">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="68557-129">Поддерживаются следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="68557-129">Supports:</span></span>
  * <span data-ttu-id="68557-130">уведомления об изменениях;</span><span class="sxs-lookup"><span data-stu-id="68557-130">Change notifications</span></span>
  * <span data-ttu-id="68557-131">[именованные параметры](#named-options-support-with-iconfigurenamedoptions);</span><span class="sxs-lookup"><span data-stu-id="68557-131">[Named options](#named-options-support-with-iconfigurenamedoptions)</span></span>
  * <span data-ttu-id="68557-132">[перезагружаемые конфигурации](#ios);</span><span class="sxs-lookup"><span data-stu-id="68557-132">[Reloadable configuration](#ios)</span></span>
  * <span data-ttu-id="68557-133">объявление определенных параметров недействительными (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>).</span><span class="sxs-lookup"><span data-stu-id="68557-133">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="68557-134">Сценарии [пост-конфигурации](#options-post-configuration) позволяют задать или изменить параметры после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-134">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="68557-135">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsFactory%601> отвечает за создание экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="68557-136">Он имеет единственный метод <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="68557-136">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="68557-137">При реализации по умолчанию принимаются все зарегистрированные интерфейсы <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Также сначала выполняются все основные настройки, а затем действия после конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-137">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="68557-138">Она различает интерфейсы <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> и <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и вызывает только соответствующий интерфейс.</span><span class="sxs-lookup"><span data-stu-id="68557-138">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="68557-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> используется интерфейсом <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для записи экземпляров `TOptions` в кэш.</span><span class="sxs-lookup"><span data-stu-id="68557-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="68557-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> делает экземпляры параметров в мониторе недействительными, что приводит к повторному вычислению значений (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="68557-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="68557-141">Значения можно также вводить вручную с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="68557-141">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="68557-142">Метод <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> используется, если необходимо повторно создать все именованные экземпляры по требованию.</span><span class="sxs-lookup"><span data-stu-id="68557-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="68557-143">Использование IOptionsSnapshot для чтения обновленных данных</span><span class="sxs-lookup"><span data-stu-id="68557-143">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="68557-144">С помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> параметры вычисляются один раз на каждый запрос при обращении к ним и кэшируются на все время существования запроса.</span><span class="sxs-lookup"><span data-stu-id="68557-144">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="68557-145">Изменения конфигурации считываются после запуска приложения при использовании поставщиков конфигурации, поддерживающих чтение обновленных значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-145">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="68557-146">Разница между `IOptionsMonitor` и `IOptionsSnapshot`:</span><span class="sxs-lookup"><span data-stu-id="68557-146">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="68557-147">`IOptionsMonitor` — это [одноэлементная служба](xref:fundamentals/dependency-injection#singleton), которая получает текущие значения параметров в любое время, что особенно полезно в одноэлементных зависимостях.</span><span class="sxs-lookup"><span data-stu-id="68557-147">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="68557-148">`IOptionsSnapshot` — это [служба с заданной областью действия](xref:fundamentals/dependency-injection#scoped), предоставляющая моментальный снимок параметров на момент создания объекта `IOptionsSnapshot<T>`.</span><span class="sxs-lookup"><span data-stu-id="68557-148">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="68557-149">Моментальные снимки параметров предназначены для использования с временными зависимостями и зависимостями с заданной областью действия.</span><span class="sxs-lookup"><span data-stu-id="68557-149">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="68557-150">В приведенном ниже коде используется <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-150">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="68557-151">Следующий код регистрирует экземпляр конфигурации, к которому привязывается `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-151">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="68557-152">В приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, считываются.</span><span class="sxs-lookup"><span data-stu-id="68557-152">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="68557-153">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="68557-153">IOptionsMonitor</span></span>

<span data-ttu-id="68557-154">Следующий код регистрирует экземпляр конфигурации, к которому привязывается `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-154">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="68557-155">В следующем примере используется <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-155">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="68557-156">По умолчанию в приведенном выше коде изменения в файле конфигурации JSON, внесенные после запуска приложения, считываются.</span><span class="sxs-lookup"><span data-stu-id="68557-156">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="68557-157">Поддержка именованных параметров с использованием IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="68557-157">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="68557-158">Именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="68557-158">Named options:</span></span>

* <span data-ttu-id="68557-159">полезны, если несколько разделов конфигурации привязываются к одним и тем же свойствам;</span><span class="sxs-lookup"><span data-stu-id="68557-159">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="68557-160">используются с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="68557-160">Are case sensitive.</span></span>

<span data-ttu-id="68557-161">Рассмотрим следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="68557-161">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="68557-162">Вместо создания двух классов для привязки `TopItem:Month` и `TopItem:Year` для каждого раздела используется следующий класс:</span><span class="sxs-lookup"><span data-stu-id="68557-162">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="68557-163">Следующий код служит для настройки именованных параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-163">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="68557-164">Следующий код отображает именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="68557-164">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="68557-165">Все параметры являются именованными экземплярами.</span><span class="sxs-lookup"><span data-stu-id="68557-165">All options are named instances.</span></span> <span data-ttu-id="68557-166">Экземпляры <xref:Microsoft.Extensions.Options.IConfigureOptions%601> считаются нацеленными на экземпляр `Options.DefaultName`, который имеет значение `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="68557-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="68557-167">Интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> также реализует интерфейс <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="68557-168">Реализация <xref:Microsoft.Extensions.Options.IOptionsFactory%601> по умолчанию содержит логику для надлежащего использования каждого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="68557-168">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="68557-169">Именованный параметр `null` предназначен для всех именованных экземпляров, а не для какого-то определенного.</span><span class="sxs-lookup"><span data-stu-id="68557-169">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="68557-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> и <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> следуют этому соглашению.</span><span class="sxs-lookup"><span data-stu-id="68557-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="68557-171">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="68557-171">OptionsBuilder API</span></span>

<span data-ttu-id="68557-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> используется для настройки экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="68557-173">`OptionsBuilder` упрощает создание именованных параметров, так как он является единственным параметром для первоначального вызова `AddOptions<TOptions>(string optionsName)` и не должен появляться во всех последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="68557-173">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="68557-174">Проверка параметров и перегрузки `ConfigureOptions`, принимающие зависимости службы, доступны только через `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="68557-174">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="68557-175">`OptionsBuilder` используется в разделе [Проверка параметров](#val).</span><span class="sxs-lookup"><span data-stu-id="68557-175">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="68557-176">Использование служб внедрения зависимостей для настройки параметров</span><span class="sxs-lookup"><span data-stu-id="68557-176">Use DI services to configure options</span></span>

<span data-ttu-id="68557-177">Использовать службы, доступные в результате внедрения зависимостей при настройке параметров, можно двумя способами.</span><span class="sxs-lookup"><span data-stu-id="68557-177">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="68557-178">Передайте делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) в [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="68557-178">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="68557-179">`OptionsBuilder<TOptions>` предоставляет перегрузки метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), которые позволяют использовать до пяти служб для настройки параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-179">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="68557-180">Создайте тип, реализующий <xref:Microsoft.Extensions.Options.IConfigureOptions%601> или <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, и зарегистрируйте этот тип как службу.</span><span class="sxs-lookup"><span data-stu-id="68557-180">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="68557-181">Рекомендуем передать делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), так как создать службу сложнее.</span><span class="sxs-lookup"><span data-stu-id="68557-181">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="68557-182">Создание типа эквивалентно тому, что делает платформа при вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="68557-182">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="68557-183">При вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) регистрируется временный универсальный интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, имеющий конструктор, который принимает указанные универсальные типы службы.</span><span class="sxs-lookup"><span data-stu-id="68557-183">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="68557-184">Проверка параметров</span><span class="sxs-lookup"><span data-stu-id="68557-184">Options validation</span></span>

<span data-ttu-id="68557-185">Проверка параметров позволяет проверять значения параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-185">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="68557-186">Рассмотрим следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="68557-186">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="68557-187">Следующий класс привязывается к разделу конфигурации `"MyConfig"` и применяет несколько правил `DataAnnotations`:</span><span class="sxs-lookup"><span data-stu-id="68557-187">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="68557-188">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="68557-188">The following code:</span></span>

* <span data-ttu-id="68557-189">вызывает <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>, чтобы получить класс [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1), который привязывается к классу `MyConfigOptions`;</span><span class="sxs-lookup"><span data-stu-id="68557-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="68557-190">вызывает <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> для включения проверки с помощью `DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="68557-190">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="68557-191">Метод расширения `ValidateDataAnnotations` определен в пакете NuGet [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="68557-191">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="68557-192">Для веб-приложений, использующих пакет SDK `Microsoft.NET.Sdk.Web`, ссылка на этот пакет указывается неявным образом из общей платформы.</span><span class="sxs-lookup"><span data-stu-id="68557-192">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="68557-193">Следующий код отображает значения конфигурации или ошибки проверки:</span><span class="sxs-lookup"><span data-stu-id="68557-193">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="68557-194">Следующий код применяет более сложное правило проверки с использованием делегата:</span><span class="sxs-lookup"><span data-stu-id="68557-194">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="68557-195">IValidateOptions для сложной проверки</span><span class="sxs-lookup"><span data-stu-id="68557-195">IValidateOptions for complex validation</span></span>

<span data-ttu-id="68557-196">Следующий класс реализует <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span><span class="sxs-lookup"><span data-stu-id="68557-196">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="68557-197">`IValidateOptions` позволяет переместить код проверки из `StartUp` в класс.</span><span class="sxs-lookup"><span data-stu-id="68557-197">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="68557-198">С использованием приведенного выше кода проверка включается в `Startup.ConfigureServices` с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="68557-198">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="68557-199">Пост-конфигурация параметров</span><span class="sxs-lookup"><span data-stu-id="68557-199">Options post-configuration</span></span>

<span data-ttu-id="68557-200">Задайте пост-конфигурацию с помощью <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-200">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="68557-201">Пост-конфигурация применяется после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-201">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="68557-202">Для последующей настройки именованных параметров доступен метод <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>.</span><span class="sxs-lookup"><span data-stu-id="68557-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="68557-203">Для последующей настройки всех экземпляров конфигурации служит метод <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="68557-203">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="68557-204">Доступ к параметрам во время запуска</span><span class="sxs-lookup"><span data-stu-id="68557-204">Accessing options during startup</span></span>

<span data-ttu-id="68557-205"><xref:Microsoft.Extensions.Options.IOptions%601> и <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> можно использовать в методе `Startup.Configure`, так как службы создаются до выполнения метода `Configure`.</span><span class="sxs-lookup"><span data-stu-id="68557-205"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="68557-206">Не используйте <xref:Microsoft.Extensions.Options.IOptions%601> или <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68557-206">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68557-207">Из-за очередности регистрации служб состояние параметров может быть несогласованным.</span><span class="sxs-lookup"><span data-stu-id="68557-207">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="68557-208">Пакет NuGet Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="68557-208">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="68557-209">Пакет [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) неявно упоминается в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68557-209">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="68557-210">Шаблон параметров использует классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="68557-210">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="68557-211">Когда [параметры конфигурации](xref:fundamentals/configuration/index) изолируются по сценарию в отдельных классах, в приложениях соблюдаются два важных принципа программной инженерии.</span><span class="sxs-lookup"><span data-stu-id="68557-211">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="68557-212">[Принцип разделения интерфейса (ISP) или инкапсуляция.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) Сценарии (классы), которые зависят от параметров конфигурации, зависят только от используемых ими параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-212">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="68557-213">[Разделение областей ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Параметры для разных частей приложения не зависят друг от друга и не связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="68557-213">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="68557-214">В параметрах также предусмотрен механизм для проверки данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-214">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="68557-215">Дополнительные сведения см. в разделе [Проверка параметров](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="68557-215">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="68557-216">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/options/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68557-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68557-217">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="68557-217">Prerequisites</span></span>

<span data-ttu-id="68557-218">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="68557-218">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="68557-219">Интерфейсы параметров</span><span class="sxs-lookup"><span data-stu-id="68557-219">Options interfaces</span></span>

<span data-ttu-id="68557-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> используется для извлечения параметров и управления уведомлениями о параметрах для экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="68557-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> поддерживается в следующих сценариях:</span><span class="sxs-lookup"><span data-stu-id="68557-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="68557-222">уведомления об изменениях;</span><span class="sxs-lookup"><span data-stu-id="68557-222">Change notifications</span></span>
* <span data-ttu-id="68557-223">[именованные параметры](#named-options-support-with-iconfigurenamedoptions);</span><span class="sxs-lookup"><span data-stu-id="68557-223">[Named options](#named-options-support-with-iconfigurenamedoptions)</span></span>
* <span data-ttu-id="68557-224">[перезагружаемые конфигурации](#reload-configuration-data-with-ioptionssnapshot);</span><span class="sxs-lookup"><span data-stu-id="68557-224">[Reloadable configuration](#reload-configuration-data-with-ioptionssnapshot)</span></span>
* <span data-ttu-id="68557-225">объявление определенных параметров недействительными (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>).</span><span class="sxs-lookup"><span data-stu-id="68557-225">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="68557-226">Сценарии [пост-конфигурации](#options-post-configuration) позволяют задать или изменить параметры после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-226">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="68557-227">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsFactory%601> отвечает за создание экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="68557-228">Он имеет единственный метод <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="68557-228">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="68557-229">При реализации по умолчанию принимаются все зарегистрированные интерфейсы <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Также сначала выполняются все основные настройки, а затем действия после конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-229">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="68557-230">Она различает интерфейсы <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> и <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и вызывает только соответствующий интерфейс.</span><span class="sxs-lookup"><span data-stu-id="68557-230">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="68557-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> используется интерфейсом <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для записи экземпляров `TOptions` в кэш.</span><span class="sxs-lookup"><span data-stu-id="68557-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="68557-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> делает экземпляры параметров в мониторе недействительными, что приводит к повторному вычислению значений (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="68557-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="68557-233">Значения можно также вводить вручную с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="68557-233">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="68557-234">Метод <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> используется, если необходимо повторно создать все именованные экземпляры по требованию.</span><span class="sxs-lookup"><span data-stu-id="68557-234">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="68557-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> полезно использовать в сценариях, когда параметры должны заново вычисляться при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="68557-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="68557-236">Дополнительные сведения см. в разделе [Повторная загрузка данных конфигурации с помощью IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="68557-236">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="68557-237"><xref:Microsoft.Extensions.Options.IOptions%601> можно использовать, чтобы обеспечить поддержку определенных параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-237"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="68557-238">Но <xref:Microsoft.Extensions.Options.IOptions%601> не поддерживается в описанных выше сценариях <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-238">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="68557-239">Вы можете продолжать использовать <xref:Microsoft.Extensions.Options.IOptions%601> в существующих платформах и библиотеках, которые уже используют интерфейс <xref:Microsoft.Extensions.Options.IOptions%601>. В таком случае вам не потребуются сценарии, предоставляемые <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-239">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="68557-240">Конфигурация общих параметров</span><span class="sxs-lookup"><span data-stu-id="68557-240">General options configuration</span></span>

<span data-ttu-id="68557-241">Конфигурация общих параметров демонстрируется в примере 1 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-241">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="68557-242">Класс параметров должен быть неабстрактным с открытым конструктором без параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-242">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="68557-243">Приведенный ниже класс (`MyOptions`) имеет два свойства: `Option1` и `Option2`.</span><span class="sxs-lookup"><span data-stu-id="68557-243">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="68557-244">Задавать значения по умолчанию необязательно, однако конструктор класса в этом примере задает значение по умолчанию для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="68557-244">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="68557-245">Значение по умолчанию для `Option2` устанавливается путем прямой инициализации (*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="68557-245">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="68557-246">Класс `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-246">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="68557-247">В следующей страничной модели применяется [внедрение зависимостей конструктора](xref:mvc/controllers/dependency-injection) с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для доступа к параметрам (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-247">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="68557-248">В файле *appsettings.json* примера задаются значения `option1` и `option2`:</span><span class="sxs-lookup"><span data-stu-id="68557-248">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="68557-249">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-249">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="68557-250">При использовании настраиваемого компонента <xref:System.Configuration.ConfigurationBuilder> для загрузки конфигурации параметров из файла настроек необходимо убедиться, что базовый путь задан правильно:</span><span class="sxs-lookup"><span data-stu-id="68557-250">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="68557-251">При загрузке конфигурации параметров из файла настроек с помощью <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> явно задавать базовый путь не требуется.</span><span class="sxs-lookup"><span data-stu-id="68557-251">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="68557-252">Настройка простых параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="68557-252">Configure simple options with a delegate</span></span>

<span data-ttu-id="68557-253">Настройка простых параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-253">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="68557-254">Используйте делегат для задания значений параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-254">Use a delegate to set options values.</span></span> <span data-ttu-id="68557-255">В образце приложения используется класс `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-255">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="68557-256">В приведенном ниже коде в контейнер службы добавляется еще одна служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-256">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="68557-257">Она использует делегат для настройки привязки к `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="68557-257">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="68557-258">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="68557-258">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="68557-259">Можно добавить несколько поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-259">You can add multiple configuration providers.</span></span> <span data-ttu-id="68557-260">Поставщики конфигурации доступны в пакетах NuGet и применяются в порядке регистрации.</span><span class="sxs-lookup"><span data-stu-id="68557-260">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="68557-261">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="68557-261">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="68557-262">При каждом вызове <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601> добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="68557-262">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="68557-263">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json* , однако значения `Option1` и `Option2` переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="68557-263">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="68557-264">Если включено более одной службы конфигурации, последний источник конфигурации *имеет приоритет* и определяет значение конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-264">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="68557-265">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-265">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="68557-266">Конфигурация подпараметров</span><span class="sxs-lookup"><span data-stu-id="68557-266">Suboptions configuration</span></span>

<span data-ttu-id="68557-267">Конфигурация подпараметров демонстрируется в примере 3 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-267">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="68557-268">В приложениях должны создаваться классы приложений, относящиеся к определенным группам сценариев (классам).</span><span class="sxs-lookup"><span data-stu-id="68557-268">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="68557-269">Части приложения, которым требуются значения конфигурации, должны иметь доступ только к используемым ими значениям конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-269">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="68557-270">При привязке параметров к конфигурации каждое свойство, относящееся к типу параметров, привязывается к ключу конфигурации в формате `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="68557-270">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="68557-271">Например, свойство `MyOptions.Option1` привязывается к ключу `Option1`, который считывается из свойства `option1` в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="68557-271">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="68557-272">В приведенном ниже коде в контейнер службы добавляется третья служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-272">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="68557-273">Он привязывает `MySubOptions` к разделу `subsection` файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="68557-273">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="68557-274">Методу `GetSection` нужно пространство имен <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="68557-274">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="68557-275">В файле *appsettings.json* примера определяется член `subsection` с ключами для `suboption1` и `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="68557-275">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="68557-276">Класс `MySubOptions` определяет свойства `SubOption1` и `SubOption2` для хранения значений параметров (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-276">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="68557-277">Метод `OnGet` страничной модели возвращает строку со значениями параметров (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-277">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="68557-278">Когда приложение выполняется, метод `OnGet` возвращает строку со значениями класса подпараметров:</span><span class="sxs-lookup"><span data-stu-id="68557-278">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="68557-279">Внедрение параметров</span><span class="sxs-lookup"><span data-stu-id="68557-279">Options injection</span></span>

<span data-ttu-id="68557-280">Внедрение параметров демонстрируется в примере 4 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-280">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="68557-281">Внедрите <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в:</span><span class="sxs-lookup"><span data-stu-id="68557-281">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="68557-282">Страница Razor или представление MVC с директивой [`@inject`](xref:mvc/views/razor#inject) Razor.</span><span class="sxs-lookup"><span data-stu-id="68557-282">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="68557-283">Модель страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="68557-283">A page or view model.</span></span>

<span data-ttu-id="68557-284">Следующий пример из примера приложения внедряет <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в модель страницы (*Pages/index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-284">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="68557-285">В примере приложения показано, как внедрить `IOptionsMonitor<MyOptions>` с директивой `@inject`.</span><span class="sxs-lookup"><span data-stu-id="68557-285">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="68557-286">Когда запускается приложение, значения параметров появляются на отображаемой странице:</span><span class="sxs-lookup"><span data-stu-id="68557-286">When the app is run, the options values are shown in the rendered page:</span></span>

![Значения параметров Option1: value1_from_json и Option2: –1 загружаются из модели и путем внедрения в представление.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="68557-288">Повторная загрузка данных конфигурации с помощью IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="68557-288">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="68557-289">Повторная загрузка данных конфигурации с помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> демонстрируется в примере 5 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-289">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="68557-290">С помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> параметры вычисляются один раз на каждый запрос при обращении к ним и кэшируются на все время существования запроса.</span><span class="sxs-lookup"><span data-stu-id="68557-290">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="68557-291">Разница между `IOptionsMonitor` и `IOptionsSnapshot`:</span><span class="sxs-lookup"><span data-stu-id="68557-291">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="68557-292">`IOptionsMonitor` — это [одноэлементная служба](xref:fundamentals/dependency-injection#singleton), которая получает текущие значения параметров в любое время, что особенно полезно в одноэлементных зависимостях.</span><span class="sxs-lookup"><span data-stu-id="68557-292">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="68557-293">`IOptionsSnapshot` — это [служба с заданной областью действия](xref:fundamentals/dependency-injection#scoped), предоставляющая моментальный снимок параметров на момент создания объекта `IOptionsSnapshot<T>`.</span><span class="sxs-lookup"><span data-stu-id="68557-293">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="68557-294">Моментальные снимки параметров предназначены для использования с временными зависимостями и зависимостями с заданной областью действия.</span><span class="sxs-lookup"><span data-stu-id="68557-294">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="68557-295">В приведенном ниже примере демонстрируется создание экземпляра <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> после изменения файла *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="68557-295">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="68557-296">Несколько запросов к серверу возвращают константные значения, предоставляемые файлом *appsettings.json* , пока файл не изменится и конфигурация не загрузится повторно.</span><span class="sxs-lookup"><span data-stu-id="68557-296">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="68557-297">Ниже показаны начальные значения `option1` и `option2`, загруженные из файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="68557-297">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="68557-298">Измените значения в файле *appsettings.json* на `value1_from_json UPDATED` и `200`.</span><span class="sxs-lookup"><span data-stu-id="68557-298">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="68557-299">Сохраните файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="68557-299">Save the *appsettings.json* file.</span></span> <span data-ttu-id="68557-300">Обновите окно браузера, чтобы увидеть обновленные значения параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-300">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="68557-301">Поддержка именованных параметров в IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="68557-301">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="68557-302">Включение поддержки именованных параметров в <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> демонстрируется в примере 6 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-302">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="68557-303">Поддержка именованных параметров позволяет приложению различать конфигурации именованных параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-303">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="68557-304">В примере приложения именованные параметры должны быть объявлены с помощью элемента [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), который вызывает метод расширения [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="68557-304">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="68557-305">Именованные параметры вводятся с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="68557-305">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="68557-306">Пример приложения обращается к именованным параметрам с помощью метода <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-306">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="68557-307">При запуске образца приложения возвращаются именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="68557-307">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="68557-308">Значения `named_options_1` предоставляются из конфигурации, которая загружается из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="68557-308">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="68557-309">Значения `named_options_2` предоставляются следующим образом:</span><span class="sxs-lookup"><span data-stu-id="68557-309">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="68557-310">Делегатом `named_options_2` в `ConfigureServices` для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="68557-310">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="68557-311">Значение по умолчанию для `Option2` предоставляется классом `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-311">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="68557-312">Настройка всех параметров с помощью метода ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="68557-312">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="68557-313">Настройте все экземпляры параметров с помощью метода <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="68557-313">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="68557-314">Приведенный ниже код настраивает `Option1` для всех экземпляров конфигурации с общим значением.</span><span class="sxs-lookup"><span data-stu-id="68557-314">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="68557-315">Добавьте следующий код в метод `Startup.ConfigureServices` вручную:</span><span class="sxs-lookup"><span data-stu-id="68557-315">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="68557-316">Если запустить образец приложения после добавления, результат будет следующим:</span><span class="sxs-lookup"><span data-stu-id="68557-316">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="68557-317">Все параметры являются именованными экземплярами.</span><span class="sxs-lookup"><span data-stu-id="68557-317">All options are named instances.</span></span> <span data-ttu-id="68557-318">Существующие экземпляры <xref:Microsoft.Extensions.Options.IConfigureOptions%601> считаются нацеленными на экземпляр `Options.DefaultName`, который имеет значение `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="68557-318">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="68557-319">Интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> также реализует интерфейс <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="68557-320">Реализация <xref:Microsoft.Extensions.Options.IOptionsFactory%601> по умолчанию содержит логику для надлежащего использования каждого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="68557-320">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="68557-321">Именованный параметр `null` предназначен для всех именованных экземпляров, а не для какого-то определенного (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> и <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> следуют этому соглашению).</span><span class="sxs-lookup"><span data-stu-id="68557-321">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="68557-322">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="68557-322">OptionsBuilder API</span></span>

<span data-ttu-id="68557-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> используется для настройки экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="68557-324">`OptionsBuilder` упрощает создание именованных параметров, так как он является единственным параметром для первоначального вызова `AddOptions<TOptions>(string optionsName)` и не должен появляться во всех последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="68557-324">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="68557-325">Проверка параметров и перегрузки `ConfigureOptions`, принимающие зависимости службы, доступны только через `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="68557-325">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="68557-326">Использование служб внедрения зависимостей для настройки параметров</span><span class="sxs-lookup"><span data-stu-id="68557-326">Use DI services to configure options</span></span>

<span data-ttu-id="68557-327">Вы можете получить доступ к другим службам, доступным в результате внедрения зависимостей при настройке параметров, двумя способами.</span><span class="sxs-lookup"><span data-stu-id="68557-327">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="68557-328">Передайте делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) в [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="68557-328">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="68557-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) предоставляет перегрузки метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), которые позволяют использовать до пяти служб для настройки параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="68557-330">Создайте собственный тип, реализующий <xref:Microsoft.Extensions.Options.IConfigureOptions%601> или <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, и зарегистрируйте этот тип как службу.</span><span class="sxs-lookup"><span data-stu-id="68557-330">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="68557-331">Рекомендуем передать делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), так как создать службу сложнее.</span><span class="sxs-lookup"><span data-stu-id="68557-331">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="68557-332">Создание собственного типа эквивалентно тому, что делает платформа при использовании метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="68557-332">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="68557-333">При вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) регистрируется временный универсальный интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, имеющий конструктор, который принимает указанные универсальные типы службы.</span><span class="sxs-lookup"><span data-stu-id="68557-333">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="68557-334">Проверка параметров</span><span class="sxs-lookup"><span data-stu-id="68557-334">Options validation</span></span>

<span data-ttu-id="68557-335">Эта функция позволяет проверить параметры при их настройке.</span><span class="sxs-lookup"><span data-stu-id="68557-335">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="68557-336">Вызовите `Validate` с использованием метода проверки, который возвращает `true`, если параметры являются допустимыми, и `false`, если они являются недопустимыми:</span><span class="sxs-lookup"><span data-stu-id="68557-336">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="68557-337">В предыдущем примере для именованного экземпляра параметров задается `optionalOptionsName`.</span><span class="sxs-lookup"><span data-stu-id="68557-337">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="68557-338">Экземпляр параметров по умолчанию — `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="68557-338">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="68557-339">Проверка выполняется при создании экземпляра параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-339">Validation runs when the options instance is created.</span></span> <span data-ttu-id="68557-340">Экземпляр параметров гарантированно проходит проверку при первом обращении.</span><span class="sxs-lookup"><span data-stu-id="68557-340">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="68557-341">Проверка параметров не защищает от изменений, вносимых после создания экземпляра параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-341">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="68557-342">Например, параметры `IOptionsSnapshot` создаются и проверяются один раз для каждого запроса при первом обращении к параметрам.</span><span class="sxs-lookup"><span data-stu-id="68557-342">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="68557-343">Параметры `IOptionsSnapshot` не проверяются повторно при последующих попытках доступа *для этого же запроса*.</span><span class="sxs-lookup"><span data-stu-id="68557-343">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="68557-344">Метод `Validate` принимает `Func<TOptions, bool>`.</span><span class="sxs-lookup"><span data-stu-id="68557-344">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="68557-345">Чтобы полностью настроить проверку, реализуйте `IValidateOptions<TOptions>`, чтобы:</span><span class="sxs-lookup"><span data-stu-id="68557-345">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="68557-346">выполнить проверку нескольких типов параметров — `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`;</span><span class="sxs-lookup"><span data-stu-id="68557-346">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="68557-347">выполнить проверку, зависящую от другого типа параметра — `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`.</span><span class="sxs-lookup"><span data-stu-id="68557-347">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="68557-348">`IValidateOptions` проверяет:</span><span class="sxs-lookup"><span data-stu-id="68557-348">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="68557-349">определенный именованный экземпляр параметров;</span><span class="sxs-lookup"><span data-stu-id="68557-349">A specific named options instance.</span></span>
* <span data-ttu-id="68557-350">все параметры, при которых `name` имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="68557-350">All options when `name` is `null`.</span></span>

<span data-ttu-id="68557-351">Возвращает `ValidateOptionsResult` из реализации интерфейса:</span><span class="sxs-lookup"><span data-stu-id="68557-351">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="68557-352">Проверка на основе заметок к данным доступна в пакете [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) с помощью вызова метода <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> в `OptionsBuilder<TOptions>`.</span><span class="sxs-lookup"><span data-stu-id="68557-352">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="68557-353">`Microsoft.Extensions.Options.DataAnnotations` входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="68557-353">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="68557-354">Безотложная проверка (быстрое прекращение при запуске) находится на этапе рассмотрения для включения в будущие выпуски.</span><span class="sxs-lookup"><span data-stu-id="68557-354">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="68557-355">Пост-конфигурация параметров</span><span class="sxs-lookup"><span data-stu-id="68557-355">Options post-configuration</span></span>

<span data-ttu-id="68557-356">Задайте пост-конфигурацию с помощью <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-356">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="68557-357">Пост-конфигурация применяется после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-357">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="68557-358">Для последующей настройки именованных параметров доступен метод <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>.</span><span class="sxs-lookup"><span data-stu-id="68557-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="68557-359">Для последующей настройки всех экземпляров конфигурации служит метод <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="68557-359">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="68557-360">Доступ к параметрам во время запуска</span><span class="sxs-lookup"><span data-stu-id="68557-360">Accessing options during startup</span></span>

<span data-ttu-id="68557-361"><xref:Microsoft.Extensions.Options.IOptions%601> и <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> можно использовать в методе `Startup.Configure`, так как службы создаются до выполнения метода `Configure`.</span><span class="sxs-lookup"><span data-stu-id="68557-361"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="68557-362">Не используйте <xref:Microsoft.Extensions.Options.IOptions%601> или <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68557-362">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68557-363">Из-за очередности регистрации служб состояние параметров может быть несогласованным.</span><span class="sxs-lookup"><span data-stu-id="68557-363">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="68557-364">Шаблон параметров использует классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="68557-364">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="68557-365">Когда [параметры конфигурации](xref:fundamentals/configuration/index) изолируются по сценарию в отдельных классах, в приложениях соблюдаются два важных принципа программной инженерии.</span><span class="sxs-lookup"><span data-stu-id="68557-365">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="68557-366">[Принцип разделения интерфейса (ISP) или инкапсуляция.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) Сценарии (классы), которые зависят от параметров конфигурации, зависят только от используемых ими параметров конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-366">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="68557-367">[Разделение областей ответственности](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Параметры для разных частей приложения не зависят друг от друга и не связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="68557-367">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="68557-368">В параметрах также предусмотрен механизм для проверки данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-368">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="68557-369">Дополнительные сведения см. в разделе [Проверка параметров](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="68557-369">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="68557-370">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/options/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68557-370">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="68557-371">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="68557-371">Prerequisites</span></span>

<span data-ttu-id="68557-372">Добавьте ссылку на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет в пакет [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/).</span><span class="sxs-lookup"><span data-stu-id="68557-372">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="68557-373">Интерфейсы параметров</span><span class="sxs-lookup"><span data-stu-id="68557-373">Options interfaces</span></span>

<span data-ttu-id="68557-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> используется для извлечения параметров и управления уведомлениями о параметрах для экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="68557-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> поддерживается в следующих сценариях:</span><span class="sxs-lookup"><span data-stu-id="68557-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="68557-376">уведомления об изменениях;</span><span class="sxs-lookup"><span data-stu-id="68557-376">Change notifications</span></span>
* <span data-ttu-id="68557-377">[именованные параметры](#named-options-support-with-iconfigurenamedoptions);</span><span class="sxs-lookup"><span data-stu-id="68557-377">[Named options](#named-options-support-with-iconfigurenamedoptions)</span></span>
* <span data-ttu-id="68557-378">[перезагружаемые конфигурации](#reload-configuration-data-with-ioptionssnapshot);</span><span class="sxs-lookup"><span data-stu-id="68557-378">[Reloadable configuration](#reload-configuration-data-with-ioptionssnapshot)</span></span>
* <span data-ttu-id="68557-379">объявление определенных параметров недействительными (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>).</span><span class="sxs-lookup"><span data-stu-id="68557-379">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="68557-380">Сценарии [пост-конфигурации](#options-post-configuration) позволяют задать или изменить параметры после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-380">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="68557-381">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsFactory%601> отвечает за создание экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="68557-382">Он имеет единственный метод <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>.</span><span class="sxs-lookup"><span data-stu-id="68557-382">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="68557-383">При реализации по умолчанию принимаются все зарегистрированные интерфейсы <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Также сначала выполняются все основные настройки, а затем действия после конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-383">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="68557-384">Она различает интерфейсы <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> и <xref:Microsoft.Extensions.Options.IConfigureOptions%601> и вызывает только соответствующий интерфейс.</span><span class="sxs-lookup"><span data-stu-id="68557-384">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="68557-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> используется интерфейсом <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для записи экземпляров `TOptions` в кэш.</span><span class="sxs-lookup"><span data-stu-id="68557-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="68557-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> делает экземпляры параметров в мониторе недействительными, что приводит к повторному вычислению значений (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="68557-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="68557-387">Значения можно также вводить вручную с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span><span class="sxs-lookup"><span data-stu-id="68557-387">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="68557-388">Метод <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> используется, если необходимо повторно создать все именованные экземпляры по требованию.</span><span class="sxs-lookup"><span data-stu-id="68557-388">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="68557-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> полезно использовать в сценариях, когда параметры должны заново вычисляться при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="68557-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="68557-390">Дополнительные сведения см. в разделе [Повторная загрузка данных конфигурации с помощью IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="68557-390">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="68557-391"><xref:Microsoft.Extensions.Options.IOptions%601> можно использовать, чтобы обеспечить поддержку определенных параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-391"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="68557-392">Но <xref:Microsoft.Extensions.Options.IOptions%601> не поддерживается в описанных выше сценариях <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-392">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="68557-393">Вы можете продолжать использовать <xref:Microsoft.Extensions.Options.IOptions%601> в существующих платформах и библиотеках, которые уже используют интерфейс <xref:Microsoft.Extensions.Options.IOptions%601>. В таком случае вам не потребуются сценарии, предоставляемые <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-393">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="68557-394">Конфигурация общих параметров</span><span class="sxs-lookup"><span data-stu-id="68557-394">General options configuration</span></span>

<span data-ttu-id="68557-395">Конфигурация общих параметров демонстрируется в примере 1 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-395">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="68557-396">Класс параметров должен быть неабстрактным с открытым конструктором без параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-396">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="68557-397">Приведенный ниже класс (`MyOptions`) имеет два свойства: `Option1` и `Option2`.</span><span class="sxs-lookup"><span data-stu-id="68557-397">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="68557-398">Задавать значения по умолчанию необязательно, однако конструктор класса в этом примере задает значение по умолчанию для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="68557-398">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="68557-399">Значение по умолчанию для `Option2` устанавливается путем прямой инициализации (*Models/MyOptions.cs*).</span><span class="sxs-lookup"><span data-stu-id="68557-399">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="68557-400">Класс `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-400">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="68557-401">В следующей страничной модели применяется [внедрение зависимостей конструктора](xref:mvc/controllers/dependency-injection) с помощью <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> для доступа к параметрам (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-401">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="68557-402">В файле *appsettings.json* примера задаются значения `option1` и `option2`:</span><span class="sxs-lookup"><span data-stu-id="68557-402">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="68557-403">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-403">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="68557-404">При использовании настраиваемого компонента <xref:System.Configuration.ConfigurationBuilder> для загрузки конфигурации параметров из файла настроек необходимо убедиться, что базовый путь задан правильно:</span><span class="sxs-lookup"><span data-stu-id="68557-404">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="68557-405">При загрузке конфигурации параметров из файла настроек с помощью <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> явно задавать базовый путь не требуется.</span><span class="sxs-lookup"><span data-stu-id="68557-405">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="68557-406">Настройка простых параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="68557-406">Configure simple options with a delegate</span></span>

<span data-ttu-id="68557-407">Настройка простых параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-407">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="68557-408">Используйте делегат для задания значений параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-408">Use a delegate to set options values.</span></span> <span data-ttu-id="68557-409">В образце приложения используется класс `MyOptionsWithDelegateConfig` (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-409">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="68557-410">В приведенном ниже коде в контейнер службы добавляется еще одна служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-410">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="68557-411">Она использует делегат для настройки привязки к `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="68557-411">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="68557-412">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="68557-412">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="68557-413">Можно добавить несколько поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-413">You can add multiple configuration providers.</span></span> <span data-ttu-id="68557-414">Поставщики конфигурации доступны в пакетах NuGet и применяются в порядке регистрации.</span><span class="sxs-lookup"><span data-stu-id="68557-414">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="68557-415">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="68557-415">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="68557-416">При каждом вызове <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601> добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="68557-416">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="68557-417">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json* , однако значения `Option1` и `Option2` переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="68557-417">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="68557-418">Если включено более одной службы конфигурации, последний источник конфигурации *имеет приоритет* и определяет значение конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-418">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="68557-419">Когда приложение выполняется, метод `OnGet` страничной модели возвращает строку со значениями класса параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-419">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="68557-420">Конфигурация подпараметров</span><span class="sxs-lookup"><span data-stu-id="68557-420">Suboptions configuration</span></span>

<span data-ttu-id="68557-421">Конфигурация подпараметров демонстрируется в примере 3 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-421">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="68557-422">В приложениях должны создаваться классы приложений, относящиеся к определенным группам сценариев (классам).</span><span class="sxs-lookup"><span data-stu-id="68557-422">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="68557-423">Части приложения, которым требуются значения конфигурации, должны иметь доступ только к используемым ими значениям конфигурации.</span><span class="sxs-lookup"><span data-stu-id="68557-423">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="68557-424">При привязке параметров к конфигурации каждое свойство, относящееся к типу параметров, привязывается к ключу конфигурации в формате `property[:sub-property:]`.</span><span class="sxs-lookup"><span data-stu-id="68557-424">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="68557-425">Например, свойство `MyOptions.Option1` привязывается к ключу `Option1`, который считывается из свойства `option1` в файле *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="68557-425">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="68557-426">В приведенном ниже коде в контейнер службы добавляется третья служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-426">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="68557-427">Он привязывает `MySubOptions` к разделу `subsection` файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="68557-427">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="68557-428">Методу `GetSection` нужно пространство имен <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="68557-428">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="68557-429">В файле *appsettings.json* примера определяется член `subsection` с ключами для `suboption1` и `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="68557-429">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="68557-430">Класс `MySubOptions` определяет свойства `SubOption1` и `SubOption2` для хранения значений параметров (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-430">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="68557-431">Метод `OnGet` страничной модели возвращает строку со значениями параметров (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-431">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="68557-432">Когда приложение выполняется, метод `OnGet` возвращает строку со значениями класса подпараметров:</span><span class="sxs-lookup"><span data-stu-id="68557-432">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="68557-433">Параметры, предоставляемые моделью представления или посредством прямого внедрения представления</span><span class="sxs-lookup"><span data-stu-id="68557-433">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="68557-434">Параметры, предоставляемые моделью представления или посредством прямого внедрения представления, демонстрируются в примере 4 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-434">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="68557-435">Параметры могут передаваться в модель представления или путем внедрения <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> непосредственно в представление (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-435">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="68557-436">В примере приложения показано, как внедрить `IOptionsMonitor<MyOptions>` с директивой `@inject`.</span><span class="sxs-lookup"><span data-stu-id="68557-436">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="68557-437">Когда запускается приложение, значения параметров появляются на отображаемой странице:</span><span class="sxs-lookup"><span data-stu-id="68557-437">When the app is run, the options values are shown in the rendered page:</span></span>

![Значения параметров Option1: value1_from_json и Option2: –1 загружаются из модели и путем внедрения в представление.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="68557-439">Повторная загрузка данных конфигурации с помощью IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="68557-439">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="68557-440">Повторная загрузка данных конфигурации с помощью <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> демонстрируется в примере 5 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-440">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="68557-441">Интерфейс <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> поддерживает повторную загрузку параметров с минимальными затратами на обработку.</span><span class="sxs-lookup"><span data-stu-id="68557-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="68557-442">Параметры вычисляются один раз на каждый запрос при обращении к ним и кэшируются на все время существования запроса.</span><span class="sxs-lookup"><span data-stu-id="68557-442">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="68557-443">В приведенном ниже примере демонстрируется создание экземпляра <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> после изменения файла *appsettings.json* (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="68557-443">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="68557-444">Несколько запросов к серверу возвращают константные значения, предоставляемые файлом *appsettings.json* , пока файл не изменится и конфигурация не загрузится повторно.</span><span class="sxs-lookup"><span data-stu-id="68557-444">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="68557-445">Ниже показаны начальные значения `option1` и `option2`, загруженные из файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="68557-445">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="68557-446">Измените значения в файле *appsettings.json* на `value1_from_json UPDATED` и `200`.</span><span class="sxs-lookup"><span data-stu-id="68557-446">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="68557-447">Сохраните файл *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="68557-447">Save the *appsettings.json* file.</span></span> <span data-ttu-id="68557-448">Обновите окно браузера, чтобы увидеть обновленные значения параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-448">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="68557-449">Поддержка именованных параметров в IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="68557-449">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="68557-450">Включение поддержки именованных параметров в <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> демонстрируется в примере 6 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="68557-450">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="68557-451">Поддержка именованных параметров позволяет приложению различать конфигурации именованных параметров.</span><span class="sxs-lookup"><span data-stu-id="68557-451">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="68557-452">В примере приложения именованные параметры должны быть объявлены с помощью элемента [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), который вызывает метод расширения [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="68557-452">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="68557-453">Именованные параметры вводятся с учетом регистра.</span><span class="sxs-lookup"><span data-stu-id="68557-453">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="68557-454">Пример приложения обращается к именованным параметрам с помощью метода <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="68557-454">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="68557-455">При запуске образца приложения возвращаются именованные параметры:</span><span class="sxs-lookup"><span data-stu-id="68557-455">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="68557-456">Значения `named_options_1` предоставляются из конфигурации, которая загружается из файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="68557-456">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="68557-457">Значения `named_options_2` предоставляются следующим образом:</span><span class="sxs-lookup"><span data-stu-id="68557-457">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="68557-458">Делегатом `named_options_2` в `ConfigureServices` для `Option1`.</span><span class="sxs-lookup"><span data-stu-id="68557-458">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="68557-459">Значение по умолчанию для `Option2` предоставляется классом `MyOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-459">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="68557-460">Настройка всех параметров с помощью метода ConfigureAll</span><span class="sxs-lookup"><span data-stu-id="68557-460">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="68557-461">Настройте все экземпляры параметров с помощью метода <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="68557-461">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="68557-462">Приведенный ниже код настраивает `Option1` для всех экземпляров конфигурации с общим значением.</span><span class="sxs-lookup"><span data-stu-id="68557-462">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="68557-463">Добавьте следующий код в метод `Startup.ConfigureServices` вручную:</span><span class="sxs-lookup"><span data-stu-id="68557-463">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="68557-464">Если запустить образец приложения после добавления, результат будет следующим:</span><span class="sxs-lookup"><span data-stu-id="68557-464">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="68557-465">Все параметры являются именованными экземплярами.</span><span class="sxs-lookup"><span data-stu-id="68557-465">All options are named instances.</span></span> <span data-ttu-id="68557-466">Существующие экземпляры <xref:Microsoft.Extensions.Options.IConfigureOptions%601> считаются нацеленными на экземпляр `Options.DefaultName`, который имеет значение `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="68557-466">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="68557-467">Интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> также реализует интерфейс <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="68557-468">Реализация <xref:Microsoft.Extensions.Options.IOptionsFactory%601> по умолчанию содержит логику для надлежащего использования каждого экземпляра.</span><span class="sxs-lookup"><span data-stu-id="68557-468">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="68557-469">Именованный параметр `null` предназначен для всех именованных экземпляров, а не для какого-то определенного (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> и <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> следуют этому соглашению).</span><span class="sxs-lookup"><span data-stu-id="68557-469">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="68557-470">API OptionsBuilder</span><span class="sxs-lookup"><span data-stu-id="68557-470">OptionsBuilder API</span></span>

<span data-ttu-id="68557-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> используется для настройки экземпляров `TOptions`.</span><span class="sxs-lookup"><span data-stu-id="68557-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="68557-472">`OptionsBuilder` упрощает создание именованных параметров, так как он является единственным параметром для первоначального вызова `AddOptions<TOptions>(string optionsName)` и не должен появляться во всех последующих вызовах.</span><span class="sxs-lookup"><span data-stu-id="68557-472">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="68557-473">Проверка параметров и перегрузки `ConfigureOptions`, принимающие зависимости службы, доступны только через `OptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="68557-473">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="68557-474">Использование служб внедрения зависимостей для настройки параметров</span><span class="sxs-lookup"><span data-stu-id="68557-474">Use DI services to configure options</span></span>

<span data-ttu-id="68557-475">Вы можете получить доступ к другим службам, доступным в результате внедрения зависимостей при настройке параметров, двумя способами.</span><span class="sxs-lookup"><span data-stu-id="68557-475">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="68557-476">Передайте делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) в [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="68557-476">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="68557-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) предоставляет перегрузки метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), которые позволяют использовать до пяти служб для настройки параметров:</span><span class="sxs-lookup"><span data-stu-id="68557-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="68557-478">Создайте собственный тип, реализующий <xref:Microsoft.Extensions.Options.IConfigureOptions%601> или <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, и зарегистрируйте этот тип как службу.</span><span class="sxs-lookup"><span data-stu-id="68557-478">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="68557-479">Рекомендуем передать делегат конфигурации методу [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), так как создать службу сложнее.</span><span class="sxs-lookup"><span data-stu-id="68557-479">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="68557-480">Создание собственного типа эквивалентно тому, что делает платформа при использовании метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="68557-480">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="68557-481">При вызове метода [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) регистрируется временный универсальный интерфейс <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, имеющий конструктор, который принимает указанные универсальные типы службы.</span><span class="sxs-lookup"><span data-stu-id="68557-481">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="68557-482">Пост-конфигурация параметров</span><span class="sxs-lookup"><span data-stu-id="68557-482">Options post-configuration</span></span>

<span data-ttu-id="68557-483">Задайте пост-конфигурацию с помощью <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-483">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="68557-484">Пост-конфигурация применяется после выполнения всех действий настройки с помощью <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="68557-484">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="68557-485">Для последующей настройки именованных параметров доступен метод <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>.</span><span class="sxs-lookup"><span data-stu-id="68557-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="68557-486">Для последующей настройки всех экземпляров конфигурации служит метод <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>.</span><span class="sxs-lookup"><span data-stu-id="68557-486">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="68557-487">Доступ к параметрам во время запуска</span><span class="sxs-lookup"><span data-stu-id="68557-487">Accessing options during startup</span></span>

<span data-ttu-id="68557-488"><xref:Microsoft.Extensions.Options.IOptions%601> и <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> можно использовать в методе `Startup.Configure`, так как службы создаются до выполнения метода `Configure`.</span><span class="sxs-lookup"><span data-stu-id="68557-488"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="68557-489">Не используйте <xref:Microsoft.Extensions.Options.IOptions%601> или <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="68557-489">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="68557-490">Из-за очередности регистрации служб состояние параметров может быть несогласованным.</span><span class="sxs-lookup"><span data-stu-id="68557-490">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="68557-491">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="68557-491">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
