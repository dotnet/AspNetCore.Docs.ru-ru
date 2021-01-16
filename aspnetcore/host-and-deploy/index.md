---
title: Размещение и развертывание ASP.NET Core
author: rick-anderson
description: Сведения о настройке сред размещения и развертывании приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 05d04a9c95910c805ea28578aba21a0658dd779a
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252972"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="22f08-103">Размещение и развертывание ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22f08-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="22f08-104">В общем при развертывании приложения ASP.NET Core в среде внешнего размещения выполняются следующие действия.</span><span class="sxs-lookup"><span data-stu-id="22f08-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="22f08-105">Развертывание опубликованного приложения в папке на сервере размещения.</span><span class="sxs-lookup"><span data-stu-id="22f08-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="22f08-106">Настройка диспетчера процессов, который запускает приложение при поступлении запросов и перезапускает его после аварийного завершения или после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="22f08-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="22f08-107">Для конфигурации настройте такой прокси-сервер, который перенаправляет запросы в приложение.</span><span class="sxs-lookup"><span data-stu-id="22f08-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="22f08-108">Публикация в папку</span><span class="sxs-lookup"><span data-stu-id="22f08-108">Publish to a folder</span></span>

<span data-ttu-id="22f08-109">Команда интерфейса командной строки [dotnet publish](/dotnet/core/tools/dotnet-publish) компилирует код приложения и копирует файлы, необходимые для его выполнения, в папку *publish*.</span><span class="sxs-lookup"><span data-stu-id="22f08-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="22f08-110">При развертывании из Visual Studio шаг `dotnet publish` выполняется автоматически перед копированием файлов место развертывания.</span><span class="sxs-lookup"><span data-stu-id="22f08-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="22f08-111">Файлы параметров публикации</span><span class="sxs-lookup"><span data-stu-id="22f08-111">Publish settings files</span></span>

<span data-ttu-id="22f08-112">Файлы `*.json` публикуются по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="22f08-112">`*.json` files are published by default.</span></span> <span data-ttu-id="22f08-113">Чтобы опубликовать другие файлы параметров, укажите их в элементе [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="22f08-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="22f08-114">В следующем примере публикуются XML-файлы:</span><span class="sxs-lookup"><span data-stu-id="22f08-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="22f08-115">Содержимое папки</span><span class="sxs-lookup"><span data-stu-id="22f08-115">Folder contents</span></span>

<span data-ttu-id="22f08-116">Папка *publish* содержит один или несколько файлов сборки и зависимости приложения, а также может включать среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="22f08-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="22f08-117">Приложения .NET Core могут публиковаться как *автономные развертывания* или *развертывания, зависящие от платформы*.</span><span class="sxs-lookup"><span data-stu-id="22f08-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="22f08-118">Если приложение автономное, в папку *publish* добавляются файлы сборки, содержащие среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="22f08-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="22f08-119">Если приложение зависит от платформы, файлы среды выполнения .NET не добавляются, так как приложение ссылается на версию .NET, установленную на сервере.</span><span class="sxs-lookup"><span data-stu-id="22f08-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="22f08-120">По умолчанию используется модель развертывания с зависимостью от платформы.</span><span class="sxs-lookup"><span data-stu-id="22f08-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="22f08-121">Дополнительные сведения см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="22f08-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="22f08-122">В дополнение к *EXE*- и *DLL*-файлам папка *публикации* для приложения ASP.NET Core обычно содержит файлы конфигурации, статические ресурсы и представления MVC.</span><span class="sxs-lookup"><span data-stu-id="22f08-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="22f08-123">Для получения дополнительной информации см. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="22f08-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="22f08-124">Настройка диспетчер процессов</span><span class="sxs-lookup"><span data-stu-id="22f08-124">Set up a process manager</span></span>

<span data-ttu-id="22f08-125">Приложение ASP.NET Core — это консольное приложение, которое должно запускаться при загрузке сервера и перезапускаться после его аварийного завершения.</span><span class="sxs-lookup"><span data-stu-id="22f08-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="22f08-126">Для автоматического запуска и перезапуска требуется диспетчер процессов.</span><span class="sxs-lookup"><span data-stu-id="22f08-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="22f08-127">Далее приведены наиболее распространенные диспетчеры процессов для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="22f08-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="22f08-128">Linux</span><span class="sxs-lookup"><span data-stu-id="22f08-128">Linux</span></span>
  * [<span data-ttu-id="22f08-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="22f08-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="22f08-130">Apache</span><span class="sxs-lookup"><span data-stu-id="22f08-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="22f08-131">Windows</span><span class="sxs-lookup"><span data-stu-id="22f08-131">Windows</span></span>
  * [<span data-ttu-id="22f08-132">Службы IIS</span><span class="sxs-lookup"><span data-stu-id="22f08-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="22f08-133">Служба Windows</span><span class="sxs-lookup"><span data-stu-id="22f08-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="22f08-134">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="22f08-134">Set up a reverse proxy</span></span>

<span data-ttu-id="22f08-135">Если приложение использует сервер [Kestrel](xref:fundamentals/servers/kestrel), вы можете использовать [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) или [IIS](xref:host-and-deploy/iis/index) в качестве обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="22f08-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="22f08-136">Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="22f08-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

::: moniker-end 

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="22f08-137">Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="22f08-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="22f08-138">Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="22f08-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"
<span data-ttu-id="22f08-139">Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="22f08-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="22f08-140">Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="22f08-140">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="22f08-141">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="22f08-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="22f08-142">Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="22f08-142">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="22f08-143">Без дополнительной настройки приложение может не иметь доступ к схеме (HTTP/HTTPS) и удаленному IP-адресу, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="22f08-143">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="22f08-144">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="22f08-144">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="22f08-145">Использование Visual Studio и MSBuild для автоматизации развертывания</span><span class="sxs-lookup"><span data-stu-id="22f08-145">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="22f08-146">Помимо копирования выходных данных из [dotnet publish](/dotnet/core/tools/dotnet-publish) на сервер в процессе развертывания часто требуется выполнение и других задач.</span><span class="sxs-lookup"><span data-stu-id="22f08-146">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="22f08-147">Например, может потребоваться включить дополнительные файлы в папку *publish* или исключить их из нее.</span><span class="sxs-lookup"><span data-stu-id="22f08-147">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="22f08-148">Visual Studio использует для веб-развертывания [MSBuild](/visualstudio/msbuild/msbuild) и настраивает MSBuild для решения многих других задач в процессе развертывания.</span><span class="sxs-lookup"><span data-stu-id="22f08-148">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="22f08-149">Дополнительные сведения см. в статье <xref:host-and-deploy/visual-studio-publish-profiles> (Профили публикации в Visual Studio) и книге [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Использование MSBuild и сборки Team Foundation).</span><span class="sxs-lookup"><span data-stu-id="22f08-149">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="22f08-150">Развертывание приложений можно выполнять напрямую из Visual Studio в службу приложений Azure, используя [компонент веб-публикации](xref:tutorials/publish-to-azure-webapp-using-vs) или [встроенную поддержку Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="22f08-150">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="22f08-151">Azure DevOps Services поддерживает [непрерывное развертывание в Службе приложений Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="22f08-151">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="22f08-152">Дополнительные сведения см. в разделе [Сборка DevOps с использованием ASP.NET Core и Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="22f08-152">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="22f08-153">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="22f08-153">Publish to Azure</span></span>

<span data-ttu-id="22f08-154">См. сведения о публикации приложения в Azure с помощью Visual Studio (<xref:tutorials/publish-to-azure-webapp-using-vs>).</span><span class="sxs-lookup"><span data-stu-id="22f08-154">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="22f08-155">Дополнительный пример приведен в статье [Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="22f08-155">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="22f08-156">Публикация с помощью MSDeploy в Windows</span><span class="sxs-lookup"><span data-stu-id="22f08-156">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="22f08-157">Инструкции о том, как опубликовать приложение с помощью профиля публикации Visual Studio или из командной строки Windows с помощью команды [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild), см. в статье <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="22f08-157">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="22f08-158">службы IIS</span><span class="sxs-lookup"><span data-stu-id="22f08-158">Internet Information Services (IIS)</span></span>

<span data-ttu-id="22f08-159">Сведения о развертывании служб IIS с конфигурацией, предоставляемой файлом *web.config*, см. в статьях в разделе <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="22f08-159">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="22f08-160">Размещение в веб-ферме</span><span class="sxs-lookup"><span data-stu-id="22f08-160">Host in a web farm</span></span>

<span data-ttu-id="22f08-161">Сведения о конфигурации для размещения приложений ASP.NET Core в среде веб-фермы (например, при развертывании множества экземпляров приложения для масштабируемости) см. в разделе <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="22f08-161">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="22f08-162">Размещение в Docker</span><span class="sxs-lookup"><span data-stu-id="22f08-162">Host on Docker</span></span>

<span data-ttu-id="22f08-163">Для получения дополнительной информации см. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="22f08-163">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="22f08-164">Выполнение проверок работоспособности</span><span class="sxs-lookup"><span data-stu-id="22f08-164">Perform health checks</span></span>

<span data-ttu-id="22f08-165">Используйте ПО промежуточного слоя для проверки работоспособности приложения и его зависимостей.</span><span class="sxs-lookup"><span data-stu-id="22f08-165">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="22f08-166">Для получения дополнительной информации см. <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="22f08-166">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22f08-167">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="22f08-167">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="22f08-168">Размещение в ASP.NET</span><span class="sxs-lookup"><span data-stu-id="22f08-168">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="22f08-169">В общем при развертывании приложения ASP.NET Core в среде внешнего размещения выполняются следующие действия.</span><span class="sxs-lookup"><span data-stu-id="22f08-169">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="22f08-170">Развертывание опубликованного приложения в папке на сервере размещения.</span><span class="sxs-lookup"><span data-stu-id="22f08-170">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="22f08-171">Настройка диспетчера процессов, который запускает приложение при поступлении запросов и перезапускает его после аварийного завершения или после перезагрузки сервера.</span><span class="sxs-lookup"><span data-stu-id="22f08-171">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="22f08-172">Для конфигурации настройте такой прокси-сервер, который перенаправляет запросы в приложение.</span><span class="sxs-lookup"><span data-stu-id="22f08-172">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="22f08-173">Публикация в папку</span><span class="sxs-lookup"><span data-stu-id="22f08-173">Publish to a folder</span></span>

<span data-ttu-id="22f08-174">Команда интерфейса командной строки [dotnet publish](/dotnet/core/tools/dotnet-publish) компилирует код приложения и копирует файлы, необходимые для его выполнения, в папку *publish*.</span><span class="sxs-lookup"><span data-stu-id="22f08-174">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="22f08-175">При развертывании из Visual Studio шаг `dotnet publish` выполняется автоматически перед копированием файлов место развертывания.</span><span class="sxs-lookup"><span data-stu-id="22f08-175">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="22f08-176">Содержимое папки</span><span class="sxs-lookup"><span data-stu-id="22f08-176">Folder contents</span></span>

<span data-ttu-id="22f08-177">Папка *publish* содержит один или несколько файлов сборки и зависимости приложения, а также может включать среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="22f08-177">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="22f08-178">Приложения .NET Core могут публиковаться как *автономные развертывания* или *развертывания, зависящие от платформы*.</span><span class="sxs-lookup"><span data-stu-id="22f08-178">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="22f08-179">Если приложение автономное, в папку *publish* добавляются файлы сборки, содержащие среду выполнения .NET.</span><span class="sxs-lookup"><span data-stu-id="22f08-179">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="22f08-180">Если приложение зависит от платформы, файлы среды выполнения .NET не добавляются, так как приложение ссылается на версию .NET, установленную на сервере.</span><span class="sxs-lookup"><span data-stu-id="22f08-180">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="22f08-181">По умолчанию используется модель развертывания с зависимостью от платформы.</span><span class="sxs-lookup"><span data-stu-id="22f08-181">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="22f08-182">Дополнительные сведения см. в статье [Развертывание приложений .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="22f08-182">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="22f08-183">В дополнение к *EXE*- и *DLL*-файлам папка *публикации* для приложения ASP.NET Core обычно содержит файлы конфигурации, статические ресурсы и представления MVC.</span><span class="sxs-lookup"><span data-stu-id="22f08-183">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="22f08-184">Для получения дополнительной информации см. <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="22f08-184">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="22f08-185">Настройка диспетчер процессов</span><span class="sxs-lookup"><span data-stu-id="22f08-185">Set up a process manager</span></span>

<span data-ttu-id="22f08-186">Приложение ASP.NET Core — это консольное приложение, которое должно запускаться при загрузке сервера и перезапускаться после его аварийного завершения.</span><span class="sxs-lookup"><span data-stu-id="22f08-186">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="22f08-187">Для автоматического запуска и перезапуска требуется диспетчер процессов.</span><span class="sxs-lookup"><span data-stu-id="22f08-187">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="22f08-188">Далее приведены наиболее распространенные диспетчеры процессов для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="22f08-188">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="22f08-189">Linux</span><span class="sxs-lookup"><span data-stu-id="22f08-189">Linux</span></span>
  * [<span data-ttu-id="22f08-190">Nginx</span><span class="sxs-lookup"><span data-stu-id="22f08-190">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="22f08-191">Apache</span><span class="sxs-lookup"><span data-stu-id="22f08-191">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="22f08-192">Windows</span><span class="sxs-lookup"><span data-stu-id="22f08-192">Windows</span></span>
  * [<span data-ttu-id="22f08-193">Службы IIS</span><span class="sxs-lookup"><span data-stu-id="22f08-193">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="22f08-194">Служба Windows</span><span class="sxs-lookup"><span data-stu-id="22f08-194">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="22f08-195">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="22f08-195">Set up a reverse proxy</span></span>

<span data-ttu-id="22f08-196">Если приложение использует сервер [Kestrel](xref:fundamentals/servers/kestrel), вы можете использовать [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) или [IIS](xref:host-and-deploy/iis/index) в качестве обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="22f08-196">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="22f08-197">Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="22f08-197">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="22f08-198">Любая из этих конфигураций &mdash; с обратным прокси-сервером и без него &mdash; является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="22f08-198">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="22f08-199">Дополнительные сведения см. в статье [Использование Kestrel с обратным прокси-сервером](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="22f08-199">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="22f08-200">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="22f08-200">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="22f08-201">Для приложений, размещенных за прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="22f08-201">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="22f08-202">Без дополнительной настройки приложение может не иметь доступ к схеме (HTTP/HTTPS) и удаленному IP-адресу, где был сформирован запрос.</span><span class="sxs-lookup"><span data-stu-id="22f08-202">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="22f08-203">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="22f08-203">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="22f08-204">Использование Visual Studio и MSBuild для автоматизации развертывания</span><span class="sxs-lookup"><span data-stu-id="22f08-204">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="22f08-205">Помимо копирования выходных данных из [dotnet publish](/dotnet/core/tools/dotnet-publish) на сервер в процессе развертывания часто требуется выполнение и других задач.</span><span class="sxs-lookup"><span data-stu-id="22f08-205">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="22f08-206">Например, может потребоваться включить дополнительные файлы в папку *publish* или исключить их из нее.</span><span class="sxs-lookup"><span data-stu-id="22f08-206">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="22f08-207">Visual Studio использует для веб-развертывания MSBuild и настраивает MSBuild для решения многих других задач в процессе развертывания.</span><span class="sxs-lookup"><span data-stu-id="22f08-207">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="22f08-208">Дополнительные сведения см. в статье <xref:host-and-deploy/visual-studio-publish-profiles> (Профили публикации в Visual Studio) и книге [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) (Использование MSBuild и сборки Team Foundation).</span><span class="sxs-lookup"><span data-stu-id="22f08-208">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="22f08-209">Развертывание приложений можно выполнять напрямую из Visual Studio в службу приложений Azure, используя [компонент веб-публикации](xref:tutorials/publish-to-azure-webapp-using-vs) или [встроенную поддержку Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment).</span><span class="sxs-lookup"><span data-stu-id="22f08-209">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="22f08-210">Azure DevOps Services поддерживает [непрерывное развертывание в Службе приложений Azure](/azure/devops/pipelines/targets/webapp).</span><span class="sxs-lookup"><span data-stu-id="22f08-210">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="22f08-211">Дополнительные сведения см. в разделе [Сборка DevOps с использованием ASP.NET Core и Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="22f08-211">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="22f08-212">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="22f08-212">Publish to Azure</span></span>

<span data-ttu-id="22f08-213">См. сведения о публикации приложения в Azure с помощью Visual Studio (<xref:tutorials/publish-to-azure-webapp-using-vs>).</span><span class="sxs-lookup"><span data-stu-id="22f08-213">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="22f08-214">Дополнительный пример приведен в статье [Создание веб-приложения ASP.NET Core в Azure](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="22f08-214">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="22f08-215">Публикация с помощью MSDeploy в Windows</span><span class="sxs-lookup"><span data-stu-id="22f08-215">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="22f08-216">Инструкции о том, как опубликовать приложение с помощью профиля публикации Visual Studio или из командной строки Windows с помощью команды [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild), см. в статье <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="22f08-216">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="22f08-217">службы IIS</span><span class="sxs-lookup"><span data-stu-id="22f08-217">Internet Information Services (IIS)</span></span>

<span data-ttu-id="22f08-218">Сведения о развертывании служб IIS с конфигурацией, предоставляемой файлом *web.config*, см. в статьях в разделе <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="22f08-218">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="22f08-219">Размещение в веб-ферме</span><span class="sxs-lookup"><span data-stu-id="22f08-219">Host in a web farm</span></span>

<span data-ttu-id="22f08-220">Сведения о конфигурации для размещения приложений ASP.NET Core в среде веб-фермы (например, при развертывании множества экземпляров приложения для масштабируемости) см. в разделе <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="22f08-220">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="22f08-221">Размещение в Docker</span><span class="sxs-lookup"><span data-stu-id="22f08-221">Host on Docker</span></span>

<span data-ttu-id="22f08-222">Для получения дополнительной информации см. <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="22f08-222">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="22f08-223">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="22f08-223">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="22f08-224">Размещение в ASP.NET</span><span class="sxs-lookup"><span data-stu-id="22f08-224">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
