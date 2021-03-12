---
title: Метапакет Microsoft.AspNetCore.App для ASP.NET Core
author: Rick-Anderson
description: Общая платформа Microsoft.AspNetCore.App
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 5d9d9cd446a61cc3e573712a4626af04dc284e99
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589586"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="8c087-103">Метапакет Microsoft.AspNetCore.App для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c087-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="8c087-104">Общая платформа ASP.NET Core (`Microsoft.AspNetCore.App`) содержит сборки, разработанные и поддерживаемые корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="8c087-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="8c087-105">`Microsoft.AspNetCore.App` устанавливается при установке [пакета SDK для .NET Core 3.0 или более поздней версии](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="8c087-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="8c087-106">*Общая платформа* — это набор сборок (*DLL*-файлы), которые установлены на компьютере, содержащий компонент среды выполнения и целевой пакет.</span><span class="sxs-lookup"><span data-stu-id="8c087-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="8c087-107">Дополнительную информацию см. в этой публикации об [общей платформе](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="8c087-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="8c087-108">Проекты, предназначенные для пакета SDK `Microsoft.NET.Sdk.Web`, неявно ссылаются на платформу `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="8c087-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="8c087-109">Для этих проектов не требуются дополнительные ссылки:</span><span class="sxs-lookup"><span data-stu-id="8c087-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="8c087-110">Общая платформа ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8c087-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="8c087-111">Не содержит зависимости сторонних разработчиков.</span><span class="sxs-lookup"><span data-stu-id="8c087-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="8c087-112">Содержит все пакеты, поддерживаемые командой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c087-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c087-113">Для этой функции нужен ASP.NET Core 2.x, нацеленный на .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8c087-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="8c087-114">[Метапакет](/dotnet/core/packages#metapackages) [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c087-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="8c087-115">Не включает сторонних зависимостей, за исключением [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) и [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="8c087-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="8c087-116">Эти зависимости необходимы для обеспечения работы основных возможностей платформ.</span><span class="sxs-lookup"><span data-stu-id="8c087-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="8c087-117">Включает все поддерживаемые командой ASP.NET Core пакеты, за исключением тех, которые содержат зависимости сторонних разработчиков (кроме указанных выше).</span><span class="sxs-lookup"><span data-stu-id="8c087-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="8c087-118">Включает все поддерживаемые командой Entity Framework Core пакеты, за исключением тех, которые содержат зависимости сторонних разработчиков (кроме указанных выше).</span><span class="sxs-lookup"><span data-stu-id="8c087-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="8c087-119">В пакет `Microsoft.AspNetCore.App` входят все компоненты ASP.NET Core 2.x и Entity Framework Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8c087-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="8c087-120">Этот пакет по умолчанию используется для шаблонов проектов, предназначенных для ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8c087-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="8c087-121">Для использования пакета `Microsoft.AspNetCore.App` рекомендуется использовать приложения, предназначенные для ASP.NET Core 2.x и Entity Framework Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8c087-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="8c087-122">Номер версии метапакета `Microsoft.AspNetCore.App` соответствует минимальной версии ASP.NET Core и версии Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="8c087-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="8c087-123">Метапакет `Microsoft.AspNetCore.App` предоставляет возможность ограничения по версиям, которые защищают приложение:</span><span class="sxs-lookup"><span data-stu-id="8c087-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="8c087-124">Если включен пакет, который имеет транзитивную (не прямую) зависимость от пакета в `Microsoft.AspNetCore.App`, и эти номера версий не совпадают, NuGet выдаст ошибку.</span><span class="sxs-lookup"><span data-stu-id="8c087-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="8c087-125">Другие пакеты, добавленные в ваше приложение не могут изменять версию пакетов, включенных в `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="8c087-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="8c087-126">Согласованность версий гарантирует надежности работы.</span><span class="sxs-lookup"><span data-stu-id="8c087-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="8c087-127">Метапакет `Microsoft.AspNetCore.App` был разработан, чтобы предотвратить использование непроверенных сочетаний версий связанных компонентов в одном приложении.</span><span class="sxs-lookup"><span data-stu-id="8c087-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="8c087-128">Приложения, использующие метапакет `Microsoft.AspNetCore.App`, автоматически получают все преимущества общей платформы ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c087-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="8c087-129">При использовании метапакета `Microsoft.AspNetCore.App` с приложением не развертываются **никакие** ресурсы из указанных по ссылке пакетов NuGet ASP.NET Core &mdash;: общая платформа .ASP.NET Core уже содержит эти ресурсы.</span><span class="sxs-lookup"><span data-stu-id="8c087-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="8c087-130">Для сокращения времени запуска приложения ресурсы в общей платформе подвергаются предварительной компиляции.</span><span class="sxs-lookup"><span data-stu-id="8c087-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="8c087-131">Дополнительную информацию см. в этой публикации об [общей платформе](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="8c087-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="8c087-132">Следующий файл проекта ссылается на метапакет `Microsoft.AspNetCore.App` для ASP.NET Core и представляет стандартный шаблон ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="8c087-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="8c087-133">Предыдущая разметка представляет типичный шаблон ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="8c087-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="8c087-134">Он не задает номер версии в ссылке на пакет `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="8c087-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="8c087-135">Если версия не указана, она задается [неявно](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) пакетом SDK, то есть пакетом `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="8c087-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="8c087-136">Рекомендуется использовать неявное указание версии через пакет SDK, а не задавать номер версии явно в ссылке на пакет.</span><span class="sxs-lookup"><span data-stu-id="8c087-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="8c087-137">Если у вас возникли вопросы по этому подходу, оставьте комментарий на GitHub в [обсуждении неявного указания версий Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="8c087-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="8c087-138">Для переносимых приложений при неявном указании версии устанавливается значение `major.minor.0`.</span><span class="sxs-lookup"><span data-stu-id="8c087-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="8c087-139">Механизм выбора последней общей платформы будет запускать приложение на последней совместимой версии среди установленных общих платформ.</span><span class="sxs-lookup"><span data-stu-id="8c087-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="8c087-140">Чтобы гарантировать, что используется одна и та же версия при разработке, тестировании и эксплуатации, убедитесь, что установлена одинаковая версия общей платформы во всех средах.</span><span class="sxs-lookup"><span data-stu-id="8c087-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="8c087-141">Для автономных приложений неявный номер версии общей платформы, включенной в установленный пакет SDK, устанавливается в значение `major.minor.patch`.</span><span class="sxs-lookup"><span data-stu-id="8c087-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="8c087-142">Указание номера версии метапакета `Microsoft.AspNetCore.App` в ссылке на него **не** гарантирует, что будет выбрана эта версия общей платформы.</span><span class="sxs-lookup"><span data-stu-id="8c087-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="8c087-143">Например, пусть указана версия 2.2.1, но установлена версия 2.2.3.</span><span class="sxs-lookup"><span data-stu-id="8c087-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="8c087-144">В этом случае приложение будет использовать версию 2.2.3.</span><span class="sxs-lookup"><span data-stu-id="8c087-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="8c087-145">Хотя это не рекомендуется, можно отключить функцию выбора последней версии (для исправлений и (или) вспомогательных версий).</span><span class="sxs-lookup"><span data-stu-id="8c087-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="8c087-146">Дополнительную информацию см. в статье о [выборе последней версии на узле .NET](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="8c087-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="8c087-147">Для `<Project Sdk` нужно установить значение `Microsoft.NET.Sdk.Web`, чтобы использовать неявную версию `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="8c087-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="8c087-148">При использовании `<Project Sdk="Microsoft.NET.Sdk">` (без `.Web` в конце) происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="8c087-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="8c087-149">Создается такое предупреждение:</span><span class="sxs-lookup"><span data-stu-id="8c087-149">The following warning is generated:</span></span>

  <span data-ttu-id="8c087-150">*Предупреждение NU1604. Зависимость проекта Microsoft.AspNetCore.App не содержит включенную нижнюю границу. Включите нижнюю границу в версию зависимости, чтобы гарантировать согласованные результаты восстановления.*</span><span class="sxs-lookup"><span data-stu-id="8c087-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="8c087-151">Это известная проблема с пакетом SDK для NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="8c087-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="8c087-152">Обновление ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c087-152">Update ASP.NET Core</span></span>

<span data-ttu-id="8c087-153"> [Метапакет](/dotnet/core/packages#metapackages)`Microsoft.AspNetCore.App` не является традиционным пакетом, который обновляется через NuGet.</span><span class="sxs-lookup"><span data-stu-id="8c087-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="8c087-154">Аналогично `Microsoft.NETCore.App`, метапакет `Microsoft.AspNetCore.App` представляет собой общую среду выполнения, которая имеет особую семантику номеров версий, обрабатываемую за пределами NuGet.</span><span class="sxs-lookup"><span data-stu-id="8c087-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="8c087-155">Дополнительную информацию см. в статье [Пакеты, метапакеты и платформы](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="8c087-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="8c087-156">Чтобы обновить ASP.NET Core, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="8c087-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="8c087-157">На компьютерах разработчиков и серверах сборки: скачайте и установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="8c087-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="8c087-158">На серверах развертывания: скачайте и установите [среду выполнения .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="8c087-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="8c087-159">Приложения будут обновлены до последней установленной версии при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="8c087-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="8c087-160">Номер версии `Microsoft.AspNetCore.App` в файле проекта обновлять не нужно.</span><span class="sxs-lookup"><span data-stu-id="8c087-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="8c087-161">Дополнительные сведения см. в разделе [Накат платформозависимых приложений](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="8c087-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="8c087-162">Если вы уже использовали `Microsoft.AspNetCore.All` в своем приложении, см. раздел [Переход от Microsoft.AspNetCore.All к Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="8c087-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
