---
title: Среда размещения ASP.NET Core в операционной системе Linux с Nginx
author: rick-anderson
description: В статье описывается процедура настройки Nginx как обратного прокси-сервера на Ubuntu 16.04 для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 6a8fd8e3498dda9b7c10834791e64df6276e2823
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253024"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="24d93-103">Среда размещения ASP.NET Core в операционной системе Linux с Nginx</span><span class="sxs-lookup"><span data-stu-id="24d93-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="24d93-104">Автор [Сурабх Ширхатти](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="24d93-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="24d93-105">В этом руководстве описывается настройка готовой к работе среды ASP.NET Core на сервере Ubuntu 16.04.</span><span class="sxs-lookup"><span data-stu-id="24d93-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="24d93-106">Эти инструкции могут подходить для более поздних версий Ubuntu, но они еще не были протестированы в этих версиях.</span><span class="sxs-lookup"><span data-stu-id="24d93-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="24d93-107">Сведения о других дистрибутивах Linux, поддерживаемых платформой ASP.NET Core, см. в разделе о [необходимых компонентах для .NET Core в Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="24d93-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="24d93-108">Для Ubuntu 14.04 в качестве решения для мониторинга процесса Kestrel рекомендуется `supervisord`.</span><span class="sxs-lookup"><span data-stu-id="24d93-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="24d93-109">Решение `systemd` в Ubuntu 14.04 недоступно.</span><span class="sxs-lookup"><span data-stu-id="24d93-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="24d93-110">Инструкции для Ubuntu 14.04 см. в [предыдущей версии этого раздела](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="24d93-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="24d93-111">В этом руководстве рассматривается</span><span class="sxs-lookup"><span data-stu-id="24d93-111">This guide:</span></span>

* <span data-ttu-id="24d93-112">размещение существующего приложения ASP.NET Core за обратным прокси-сервером;</span><span class="sxs-lookup"><span data-stu-id="24d93-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="24d93-113">настройка обратного прокси-сервера для перенаправления запросов на веб-сервер Kestrel;</span><span class="sxs-lookup"><span data-stu-id="24d93-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="24d93-114">проверка выполнения веб-приложения как управляющей программы при запуске системы;</span><span class="sxs-lookup"><span data-stu-id="24d93-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="24d93-115">настройка средства управления процессами, с помощью которого можно перезапустить веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="24d93-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="24d93-116">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="24d93-116">Prerequisites</span></span>

1. <span data-ttu-id="24d93-117">Доступ к серверу Ubuntu 16.04 под учетной записью обычного пользователя с правами sudo.</span><span class="sxs-lookup"><span data-stu-id="24d93-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="24d93-118">Установите среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="24d93-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="24d93-119">Перейдите на [страницу скачивания .NET Core](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="24d93-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="24d93-120">Выберите последнюю не предварительную версию .NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d93-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="24d93-121">Скачайте последнюю не предварительную версию среды выполнения из таблицы под заголовком **Run apps — Runtime** (Выполнение приложений — среда выполнения).</span><span class="sxs-lookup"><span data-stu-id="24d93-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="24d93-122">Щелкните ссылку **Package manager instructions** (Инструкции диспетчера пакетов) для Linux и выполните инструкции для соответствующей версии Ubuntu.</span><span class="sxs-lookup"><span data-stu-id="24d93-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="24d93-123">Существующее приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d93-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="24d93-124">Перезапустить приложения ASP.NET Core, размещенные на сервере, можно в любой момент после обновления общей платформы.</span><span class="sxs-lookup"><span data-stu-id="24d93-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="24d93-125">Публикация и копирование приложения</span><span class="sxs-lookup"><span data-stu-id="24d93-125">Publish and copy over the app</span></span>

<span data-ttu-id="24d93-126">Настройте приложение, чтобы [его развертывание зависело от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="24d93-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="24d93-127">Если приложение запускается локально и не настроено для безопасного подключения (HTTPS), следует применять один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="24d93-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="24d93-128">Настройка приложения для обработки безопасных локальных подключений.</span><span class="sxs-lookup"><span data-stu-id="24d93-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="24d93-129">Дополнительные сведения см. в разделе [Конфигурация HTTPS](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="24d93-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="24d93-130">Удалите `https://localhost:5001` (при его наличии) из свойства `applicationUrl` в файле `Properties/launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="24d93-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="24d93-131">Выполните команду [dotnet publish](/dotnet/core/tools/dotnet-publish) в среде разработки, чтобы упаковать приложение в каталог (например, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, где заполнитель `{TARGET FRAMEWORK MONIKER}` является моникером целевой платформы или TFM), который может выполняться на сервере:</span><span class="sxs-lookup"><span data-stu-id="24d93-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="24d93-132">Приложение может быть опубликовано как [автономное развертывание](/dotnet/core/deploying/#self-contained-deployments-scd), если вы предпочитаете не сохранять среду выполнения .NET Core на сервере.</span><span class="sxs-lookup"><span data-stu-id="24d93-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="24d93-133">Скопируйте приложение ASP.NET Core на сервер с помощью инструмента, интегрированного в ваш рабочий процесс (например, `SCP`, `SFTP`).</span><span class="sxs-lookup"><span data-stu-id="24d93-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="24d93-134">Обычно веб-приложения находятся в каталоге `var` (например, `var/www/helloapp`).</span><span class="sxs-lookup"><span data-stu-id="24d93-134">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="24d93-135">Если развертывание выполняется в рабочей среде, рабочий процесс непрерывной интеграции автоматически опубликует приложение и скопирует его ресурсы на сервер.</span><span class="sxs-lookup"><span data-stu-id="24d93-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="24d93-136">Проверьте работу приложения:</span><span class="sxs-lookup"><span data-stu-id="24d93-136">Test the app:</span></span>

1. <span data-ttu-id="24d93-137">Запустите приложение в командной строке: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="24d93-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="24d93-138">В браузере откройте страницу `http://<serveraddress>:<port>`, чтобы убедиться, что приложение локально работает на платформе Linux.</span><span class="sxs-lookup"><span data-stu-id="24d93-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="24d93-139">Настройка обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="24d93-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="24d93-140">Обратный прокси-сервер — это стандартный вариант настройки для обслуживания динамических веб-приложений.</span><span class="sxs-lookup"><span data-stu-id="24d93-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="24d93-141">Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d93-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="24d93-142">Использование обратного прокси-сервера</span><span class="sxs-lookup"><span data-stu-id="24d93-142">Use a reverse proxy server</span></span>

<span data-ttu-id="24d93-143">Kestrel является отличным решением для обслуживания динамического содержимого из ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d93-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="24d93-144">При этом компоненты для работы с веб-службами не настолько функциональны, как серверы типа IIS, Apache или Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d93-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="24d93-145">Обратный прокси-сервер может облегчить такую работу, как обслуживание статического содержимого, кэширование и сжатие запросов, а также терминирование HTTPS с HTTP-сервера.</span><span class="sxs-lookup"><span data-stu-id="24d93-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="24d93-146">Обратный прокси-сервер можно разместить на отдельном компьютере или развернуть параллельно с HTTP-сервером.</span><span class="sxs-lookup"><span data-stu-id="24d93-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="24d93-147">В контексте данного руководства используется отдельный экземпляр Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d93-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="24d93-148">Он выполняется на том же сервере, что и HTTP-сервер.</span><span class="sxs-lookup"><span data-stu-id="24d93-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="24d93-149">Настройки можно выбирать в зависимости от требований.</span><span class="sxs-lookup"><span data-stu-id="24d93-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="24d93-150">Так как запросы перенаправляются обратным прокси-сервером, используйте [ПО промежуточного слоя перенаправленных заголовков](xref:host-and-deploy/proxy-load-balancer) из пакета [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides).</span><span class="sxs-lookup"><span data-stu-id="24d93-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="24d93-151">Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.</span><span class="sxs-lookup"><span data-stu-id="24d93-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="24d93-152">Вызовите <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> метод наверху `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="24d93-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="24d93-153">В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:</span><span class="sxs-lookup"><span data-stu-id="24d93-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="24d93-154">Если параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не задан для ПО промежуточного слоя, по умолчанию перенаправляются заголовки `None`.</span><span class="sxs-lookup"><span data-stu-id="24d93-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="24d93-155">Прокси-серверы под управлением адресов замыкания на себя (`127.0.0.0/8`, `[::1]`), включая стандартные адреса localhost (`127.0.0.1`), считаются доверенными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="24d93-155">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="24d93-156">Если запросы между Интернетом и веб-сервером обрабатывают другие прокси-серверы или сети организации, добавьте их в список <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> или <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> с помощью <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span><span class="sxs-lookup"><span data-stu-id="24d93-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="24d93-157">Следующий пример добавляет доверенный прокси-сервер с IP-адресом 10.0.0.100 в ПО промежуточного слоя для перенаправления заголовков `KnownProxies` в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="24d93-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="24d93-158">Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="24d93-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="24d93-159">Установка Nginx</span><span class="sxs-lookup"><span data-stu-id="24d93-159">Install Nginx</span></span>

<span data-ttu-id="24d93-160">Установите Nginx с помощью команды `apt-get`.</span><span class="sxs-lookup"><span data-stu-id="24d93-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="24d93-161">Программа установки создает сценарий инициализации `systemd`, который запускает Nginx как управляющую программу при запуске системы.</span><span class="sxs-lookup"><span data-stu-id="24d93-161">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="24d93-162">Следуйте инструкциям по установке для Ubuntu в разделе [Nginx: официальные пакеты Debian и Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="24d93-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="24d93-163">Если необходимы дополнительные модули Nginx, может потребоваться создание Nginx из источника.</span><span class="sxs-lookup"><span data-stu-id="24d93-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="24d93-164">Так как Nginx устанавливается впервые, запустите его напрямую, выполнив следующую команду.</span><span class="sxs-lookup"><span data-stu-id="24d93-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="24d93-165">В браузере должна открыться стартовая страница Nginx по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="24d93-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="24d93-166">Целевая страница доступна по адресу `http://<server_IP_address>/index.nginx-debian.html`.</span><span class="sxs-lookup"><span data-stu-id="24d93-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="24d93-167">Настройка Nginx</span><span class="sxs-lookup"><span data-stu-id="24d93-167">Configure Nginx</span></span>

<span data-ttu-id="24d93-168">Чтобы настроить Nginx как обратный прокси-сервер для перенаправления HTTP-запросов в ваше приложение ASP.NET Core, измените файл `/etc/nginx/sites-available/default`.</span><span class="sxs-lookup"><span data-stu-id="24d93-168">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="24d93-169">Откройте этот файл в текстовом редакторе и замените его содержимое на следующий фрагмент кода:</span><span class="sxs-lookup"><span data-stu-id="24d93-169">Open it in a text editor, and replace the contents with the following snippet:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="24d93-170">Если вы работаете с приложением SignalR или Blazor Server, дополнительные сведения см. в разделах о <xref:signalr/scale#linux-with-nginx> и <xref:blazor/host-and-deploy/server#linux-with-nginx> соответственно.</span><span class="sxs-lookup"><span data-stu-id="24d93-170">If the app is a SignalR or Blazor Server app, see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="24d93-171">Если отсутствуют совпадения для `server_name`, Nginx использует сервер по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="24d93-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="24d93-172">Если сервер по умолчанию не определен, первый сервер в файле конфигурации является сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="24d93-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="24d93-173">Рекомендуется добавить в качестве сервера по умолчанию определенный сервер, который возвращает код состояния 444 в файле конфигурации.</span><span class="sxs-lookup"><span data-stu-id="24d93-173">As a best practice, add a specific default server that returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="24d93-174">Ниже приведен пример конфигурации сервера по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="24d93-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="24d93-175">С представленным выше файлом конфигурации и сервером по умолчанию Nginx принимает трафик от любого источника через порт 80 с заголовком узла `example.com` или `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="24d93-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="24d93-176">Запросы, не соответствующие этим узлам, не будут перенаправляться в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d93-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="24d93-177">Запросы, которые им соответствуют, Nginx перенаправляет в Kestrel по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d93-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="24d93-178">Дополнительные сведения см. в статье [Как nginx обрабатывает запросы](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="24d93-178">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="24d93-179">Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="24d93-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="24d93-180">С представленным выше файлом конфигурации и сервером по умолчанию Nginx принимает трафик от любого источника через порт 80 с заголовком узла `example.com` или `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="24d93-180">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="24d93-181">Запросы, не соответствующие этим узлам, не будут перенаправляться в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d93-181">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="24d93-182">Запросы, которые им соответствуют, Nginx перенаправляет в Kestrel по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d93-182">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="24d93-183">Дополнительные сведения см. в статье [Как nginx обрабатывает запросы](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="24d93-183">For more information, see [How nginx processes a request](https://nginx.org/docs/http/request_processing.html).</span></span> <span data-ttu-id="24d93-184">Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="24d93-184">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="24d93-185">Если не будет указана правильная [директива server_name](https://nginx.org/docs/http/server_names.html), приложение будет подвержено значительным уязвимостям.</span><span class="sxs-lookup"><span data-stu-id="24d93-185">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="24d93-186">Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.example.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость).</span><span class="sxs-lookup"><span data-stu-id="24d93-186">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="24d93-187">Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="24d93-187">For more information, see [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

<span data-ttu-id="24d93-188">Установив конфигурацию Nginx, выполните команду `sudo nginx -t`, чтобы проверить синтаксис файлов конфигурации.</span><span class="sxs-lookup"><span data-stu-id="24d93-188">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="24d93-189">Если проверка файла конфигурации прошла успешно, заставьте Nginx принять изменения, выполнив команду `sudo nginx -s reload`.</span><span class="sxs-lookup"><span data-stu-id="24d93-189">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="24d93-190">Для непосредственного запуска приложений на сервере:</span><span class="sxs-lookup"><span data-stu-id="24d93-190">To directly run the app on the server:</span></span>

1. <span data-ttu-id="24d93-191">Перейдите в каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="24d93-191">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="24d93-192">Запустите приложение: `dotnet <app_assembly.dll>`, где `app_assembly.dll` — имя файла сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="24d93-192">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="24d93-193">Если приложение выполняется на сервере, но не отвечает по Интернету, проверьте брандмауэр сервера и убедитесь, что порт 80 открыт.</span><span class="sxs-lookup"><span data-stu-id="24d93-193">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm port 80 is open.</span></span> <span data-ttu-id="24d93-194">При использовании виртуальной машины Ubuntu Azure добавьте правило группы безопасности сети (NSG), которое разрешает входящий трафик через порт 80.</span><span class="sxs-lookup"><span data-stu-id="24d93-194">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="24d93-195">Не нужно включать правило исходящего трафика на порте 80, так как исходящий трафик предоставляется автоматически при включении правила для входящего трафика.</span><span class="sxs-lookup"><span data-stu-id="24d93-195">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="24d93-196">Когда закончите тестировать приложение, завершите его работу с помощью клавиш <kbd>CTRL</kbd> + <kbd>C</kbd> в командной строке.</span><span class="sxs-lookup"><span data-stu-id="24d93-196">When done testing the app, shut down the app with <kbd>Ctrl</kbd> + <kbd>C</kbd> at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="24d93-197">Мониторинг приложения</span><span class="sxs-lookup"><span data-stu-id="24d93-197">Monitor the app</span></span>

<span data-ttu-id="24d93-198">Сервер настроен на перенаправление запросов к `http://<serveraddress>:80` в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d93-198">The server is set up to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="24d93-199">При этом Nginx не настроен для управления процессом Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d93-199">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="24d93-200">Для запуска и мониторинга соответствующего веб-приложения можно использовать `systemd` и создать файл службы.</span><span class="sxs-lookup"><span data-stu-id="24d93-200">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="24d93-201">`systemd` — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими.</span><span class="sxs-lookup"><span data-stu-id="24d93-201">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="24d93-202">Создание файла службы</span><span class="sxs-lookup"><span data-stu-id="24d93-202">Create the service file</span></span>

<span data-ttu-id="24d93-203">Создайте файл определения службы.</span><span class="sxs-lookup"><span data-stu-id="24d93-203">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="24d93-204">Далее представлен пример файла службы для нашего приложения:</span><span class="sxs-lookup"><span data-stu-id="24d93-204">The following example is a service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="24d93-205">В предыдущем примере управляющий службой пользователь задается с помощью параметра `User`.</span><span class="sxs-lookup"><span data-stu-id="24d93-205">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="24d93-206">Этот пользователь (`www-data`) должен существовать и иметь права владельца в отношении файлов приложения.</span><span class="sxs-lookup"><span data-stu-id="24d93-206">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="24d93-207">Используйте `TimeoutStopSec`, чтобы настроить время ожидания до завершения работы приложения после начального сигнала прерывания.</span><span class="sxs-lookup"><span data-stu-id="24d93-207">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="24d93-208">Если приложение не завершит работу в течение этого периода, оно прерывается сигналом SIGKILL.</span><span class="sxs-lookup"><span data-stu-id="24d93-208">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="24d93-209">Укажите значение в секундах без единиц измерения (например, `150`), значение интервала (например, `2min 30s`) или значение `infinity`, которое отключает время ожидания.</span><span class="sxs-lookup"><span data-stu-id="24d93-209">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="24d93-210">`TimeoutStopSec` по умолчанию использует значение `DefaultTimeoutStopSec` в файле конфигурации диспетчера (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span><span class="sxs-lookup"><span data-stu-id="24d93-210">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="24d93-211">В большинстве дистрибутивов по умолчанию устанавливается время ожидания 90 секунд.</span><span class="sxs-lookup"><span data-stu-id="24d93-211">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="24d93-212">Linux имеет файловую систему, в которой учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="24d93-212">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="24d93-213">Задание `ASPNETCORE_ENVIRONMENT` для `Production` приведет к поиску файла конфигурации `appsettings.Production.json`, а не `appsettings.production.json`.</span><span class="sxs-lookup"><span data-stu-id="24d93-213">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="24d93-214">Некоторые значения (например, строки подключения SQL) необходимо экранировать, чтобы поставщики конфигурации могли читать переменные среды.</span><span class="sxs-lookup"><span data-stu-id="24d93-214">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="24d93-215">Используйте следующую команду, чтобы создать правильно экранированное значение для файла конфигурации:</span><span class="sxs-lookup"><span data-stu-id="24d93-215">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24d93-216">Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды.</span><span class="sxs-lookup"><span data-stu-id="24d93-216">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="24d93-217">Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия.</span><span class="sxs-lookup"><span data-stu-id="24d93-217">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="24d93-218">[Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="24d93-218">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="24d93-219">В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="24d93-219">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24d93-220">Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды.</span><span class="sxs-lookup"><span data-stu-id="24d93-220">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="24d93-221">Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия.</span><span class="sxs-lookup"><span data-stu-id="24d93-221">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="24d93-222">[Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="24d93-222">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="24d93-223">В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.</span><span class="sxs-lookup"><span data-stu-id="24d93-223">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="24d93-224">Сохраните файл и включите службу.</span><span class="sxs-lookup"><span data-stu-id="24d93-224">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="24d93-225">Запустите службу и убедитесь, что она работает.</span><span class="sxs-lookup"><span data-stu-id="24d93-225">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="24d93-226">Теперь, когда обратный прокси-сервер настроен и `systemd`управляет процессом Kestrel, веб-приложение можно считать полностью настроенным и вы можете обратиться к нему по адресу`http://localhost` из браузера на локальном компьютере.</span><span class="sxs-lookup"><span data-stu-id="24d93-226">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="24d93-227">Оно также доступно для удаленных компьютеров, несмотря на наличие блокирующих трафик межсетевых экранов.</span><span class="sxs-lookup"><span data-stu-id="24d93-227">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="24d93-228">Заголовок `Server` в ответе подтверждает, что приложение ASP.NET Core обслуживается Kestrel.</span><span class="sxs-lookup"><span data-stu-id="24d93-228">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="24d93-229">Просмотр журналов</span><span class="sxs-lookup"><span data-stu-id="24d93-229">View logs</span></span>

<span data-ttu-id="24d93-230">Так как веб-приложение, использующее Kestrel, управляется через `systemd`, все события и процессы регистрируются в централизованном журнале.</span><span class="sxs-lookup"><span data-stu-id="24d93-230">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="24d93-231">При этом журнал содержит все записи обо всех службах и процессах, управляемых `systemd`.</span><span class="sxs-lookup"><span data-stu-id="24d93-231">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="24d93-232">Чтобы просмотреть элементы, связанные с `kestrel-helloapp.service`, используйте следующую команду.</span><span class="sxs-lookup"><span data-stu-id="24d93-232">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="24d93-233">Кроме того, количество возвращаемых записей можно уменьшить, указав параметры времени, например `--since today`, `--until 1 hour ago` или их комбинацию.</span><span class="sxs-lookup"><span data-stu-id="24d93-233">For further filtering, time options such as `--since today`, `--until 1 hour ago`, or a combination of these can reduce the number of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="24d93-234">Защита данных</span><span class="sxs-lookup"><span data-stu-id="24d93-234">Data protection</span></span>

<span data-ttu-id="24d93-235">[Стек защиты данных в ASP.NET Core](xref:security/data-protection/introduction) используется определенным [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая промежуточное ПО для проверки подлинности (например, промежуточное ПО файлов cookie) и средствами защиты от подделки межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="24d93-235">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="24d93-236">Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, необходимо настроить защиту данных для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="24d93-236">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="24d93-237">Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.</span><span class="sxs-lookup"><span data-stu-id="24d93-237">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="24d93-238">Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="24d93-238">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="24d93-239">Все токены аутентификации, использующие файлы cookie, становятся недействительными.</span><span class="sxs-lookup"><span data-stu-id="24d93-239">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="24d93-240">При выполнении следующего запроса пользователю требуется выполнить вход снова.</span><span class="sxs-lookup"><span data-stu-id="24d93-240">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="24d93-241">Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы.</span><span class="sxs-lookup"><span data-stu-id="24d93-241">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="24d93-242">Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="24d93-242">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="24d93-243">Сведения о настройке защиты данных для хранения и шифрования набора ключей см. в приведенных ниже статьях.</span><span class="sxs-lookup"><span data-stu-id="24d93-243">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="24d93-244">Длинные поля заголовка запроса</span><span class="sxs-lookup"><span data-stu-id="24d93-244">Long request header fields</span></span>

<span data-ttu-id="24d93-245">Параметры прокси-сервера по умолчанию обычно ограничивают длину полей заголовка запроса значением 4 КБ или 8 КБ (в зависимости от платформы).</span><span class="sxs-lookup"><span data-stu-id="24d93-245">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="24d93-246">Приложению могут потребоваться более длинные поля (например, приложениям, использующим [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span><span class="sxs-lookup"><span data-stu-id="24d93-246">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="24d93-247">В этом случае требуется скорректировать параметры по умолчанию для прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="24d93-247">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="24d93-248">Применяемые значения зависят от конкретного сценария.</span><span class="sxs-lookup"><span data-stu-id="24d93-248">The values to apply depend on the scenario.</span></span> <span data-ttu-id="24d93-249">Дополнительные сведения см. в документации сервера.</span><span class="sxs-lookup"><span data-stu-id="24d93-249">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="24d93-250">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="24d93-250">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="24d93-251">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="24d93-251">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="24d93-252">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="24d93-252">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="24d93-253">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="24d93-253">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="24d93-254">Не увеличивайте значение буферов прокси-сервера по умолчанию, если это не требуется.</span><span class="sxs-lookup"><span data-stu-id="24d93-254">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="24d93-255">Увеличение этих значений повышает риск переполнения буфера и атак типа "отказ в обслуживании" (DoS) со стороны злоумышленников.</span><span class="sxs-lookup"><span data-stu-id="24d93-255">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="24d93-256">Защита приложения</span><span class="sxs-lookup"><span data-stu-id="24d93-256">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="24d93-257">Включение AppArmor</span><span class="sxs-lookup"><span data-stu-id="24d93-257">Enable AppArmor</span></span>

<span data-ttu-id="24d93-258">Начиная с версии 2.6 ядро Linux включает платформу модулей безопасности Linux (LSM).</span><span class="sxs-lookup"><span data-stu-id="24d93-258">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="24d93-259">LSM поддерживают различные реализации модулей безопасности.</span><span class="sxs-lookup"><span data-stu-id="24d93-259">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="24d93-260">[AppArmor](https://wiki.ubuntu.com/AppArmor) — это LSM, который реализует систему обязательного контроля доступа, позволяющую ограничивать программу определенным набором ресурсов.</span><span class="sxs-lookup"><span data-stu-id="24d93-260">[AppArmor](https://wiki.ubuntu.com/AppArmor) is an LSM that implements a Mandatory Access Control system, which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="24d93-261">Убедитесь, что AppArmor включен и правильно настроен.</span><span class="sxs-lookup"><span data-stu-id="24d93-261">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="24d93-262">Настройка брандмауэра</span><span class="sxs-lookup"><span data-stu-id="24d93-262">Configure the firewall</span></span>

<span data-ttu-id="24d93-263">Закройте все внешние порты, которые не используются.</span><span class="sxs-lookup"><span data-stu-id="24d93-263">Close off all external ports that aren't in use.</span></span> <span data-ttu-id="24d93-264">Простой брандмауэр (ufw) позволяет взаимодействовать с `iptables`. Для настройки брандмауэра предоставляется CLI.</span><span class="sxs-lookup"><span data-stu-id="24d93-264">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="24d93-265">Неправильно настроенный брандмауэр предотвратит доступ ко всей системе.</span><span class="sxs-lookup"><span data-stu-id="24d93-265">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="24d93-266">Если задать неправильный порт SSH, то при использовании SSH для подключения к системе произойдет ее блокировка.</span><span class="sxs-lookup"><span data-stu-id="24d93-266">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="24d93-267">Порт по умолчанию — 22.</span><span class="sxs-lookup"><span data-stu-id="24d93-267">The default port is 22.</span></span> <span data-ttu-id="24d93-268">Дополнительные сведения см. в [вводной статье по ufw](https://help.ubuntu.com/community/UFW) и в [этом руководстве](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="24d93-268">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="24d93-269">Установите `ufw` и настройте его для разрешения прохождения трафика через все необходимые порты.</span><span class="sxs-lookup"><span data-stu-id="24d93-269">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="24d93-270">Защита Nginx</span><span class="sxs-lookup"><span data-stu-id="24d93-270">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="24d93-271">Изменение имени ответа Nginx</span><span class="sxs-lookup"><span data-stu-id="24d93-271">Change the Nginx response name</span></span>

<span data-ttu-id="24d93-272">Отредактируйте файл `src/http/ngx_http_header_filter_module.c`:</span><span class="sxs-lookup"><span data-stu-id="24d93-272">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="24d93-273">Настройка параметров</span><span class="sxs-lookup"><span data-stu-id="24d93-273">Configure options</span></span>

<span data-ttu-id="24d93-274">Настройте дополнительные обязательные модули на сервере.</span><span class="sxs-lookup"><span data-stu-id="24d93-274">Configure the server with additional required modules.</span></span> <span data-ttu-id="24d93-275">Для дополнительной защиты приложения можно использовать межсетевой экран для веб-приложений, например [ModSecurity](https://www.modsecurity.org/).</span><span class="sxs-lookup"><span data-stu-id="24d93-275">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="24d93-276">Конфигурация HTTPS</span><span class="sxs-lookup"><span data-stu-id="24d93-276">HTTPS configuration</span></span>

<span data-ttu-id="24d93-277">**Настройка приложения для безопасных (HTTPS) локальных подключений**</span><span class="sxs-lookup"><span data-stu-id="24d93-277">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="24d93-278">Команда [dotnet run](/dotnet/core/tools/dotnet-run) использует файл приложения *Properties/launchSettings.json*, который настраивает приложение для прослушивания URL-адресов, заданных свойством `applicationUrl`.</span><span class="sxs-lookup"><span data-stu-id="24d93-278">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property.</span></span> <span data-ttu-id="24d93-279">Например, `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d93-279">For example, `https://localhost:5001;http://localhost:5000`.</span></span>

<span data-ttu-id="24d93-280">Настройте приложение, чтобы оно использовало при разработке сертификат для команды `dotnet run` или среды разработки (<kbd>F5</kbd> или <kbd>CTRL</kbd>+<kbd>F5</kbd> в Visual Studio Code), используя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="24d93-280">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="24d93-281">[Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel/endpoints#configuration) (*рекомендуется*)</span><span class="sxs-lookup"><span data-stu-id="24d93-281">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="24d93-282">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="24d93-282">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="24d93-283">[Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel#configuration) (*рекомендуется*)</span><span class="sxs-lookup"><span data-stu-id="24d93-283">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="24d93-284">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="24d93-284">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

<span data-ttu-id="24d93-285">**Настройка обратного прокси-сервера для безопасного подключения клиентов (HTTPS)**</span><span class="sxs-lookup"><span data-stu-id="24d93-285">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="24d93-286">Настройте сервер для прослушивания трафика HTTPS через порт 443, указав действительный сертификат, выпущенный доверенным центром сертификации (ЦС).</span><span class="sxs-lookup"><span data-stu-id="24d93-286">Configure the server to listen to HTTPS traffic on port 443 by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="24d93-287">Обеспечьте дополнительную защиту, применив некоторые методы, показанные в представленном ниже файле */etc/nginx/nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="24d93-287">Harden the security by employing some of the practices depicted in the following */etc/nginx/nginx.conf* file.</span></span> <span data-ttu-id="24d93-288">Это может быть выбор более строгого шифра и перенаправление всего HTTP-трафика в HTTPS.</span><span class="sxs-lookup"><span data-stu-id="24d93-288">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="24d93-289">Для среды разработки рекомендуется использовать временные (302), а не постоянные перенаправления (301).</span><span class="sxs-lookup"><span data-stu-id="24d93-289">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="24d93-290">Кэширование ссылок может привести к нестабильной работе в средах разработки.</span><span class="sxs-lookup"><span data-stu-id="24d93-290">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="24d93-291">При добавлении заголовка `HTTP Strict-Transport-Security` (HSTS) все последующие запросы клиента будут проходить по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="24d93-291">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="24d93-292">Чтобы узнать важные рекомендации по HSTS, см. <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span><span class="sxs-lookup"><span data-stu-id="24d93-292">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="24d93-293">Если протокол HTTPS будет отключен в будущем, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="24d93-293">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="24d93-294">Не добавляйте заголовок HSTS.</span><span class="sxs-lookup"><span data-stu-id="24d93-294">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="24d93-295">Выберите короткое значение `max-age`.</span><span class="sxs-lookup"><span data-stu-id="24d93-295">Choose a short `max-age` value.</span></span>

<span data-ttu-id="24d93-296">Добавьте файл конфигурации */etc/nginx/proxy.conf*:</span><span class="sxs-lookup"><span data-stu-id="24d93-296">Add the */etc/nginx/proxy.conf* configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="24d93-297">**Замените** содержимое файла конфигурации */etc/nginx/nginx.conf* следующим файлом.</span><span class="sxs-lookup"><span data-stu-id="24d93-297">**Replace** the contents of the */etc/nginx/nginx.conf* configuration file with the following file.</span></span> <span data-ttu-id="24d93-298">В этом примере показаны разделы `http` и `server` одного и того же файла конфигурации.</span><span class="sxs-lookup"><span data-stu-id="24d93-298">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="24d93-299">Приложениям Blazor WebAssembly требуется большее значение параметра `burst` с учетом большего количества запросов, выполняемых приложением.</span><span class="sxs-lookup"><span data-stu-id="24d93-299">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="24d93-300">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="24d93-300">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="24d93-301">Защита Nginx от кликджекинга</span><span class="sxs-lookup"><span data-stu-id="24d93-301">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="24d93-302">[Кликджекинг](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger) (или *атака с подменой пользовательского интерфейса*) является вредоносной атакой, при которой посетителя сайта обманным путем вынуждают щелкнуть ссылку или нажать кнопку не той страницы, на которой он находится.</span><span class="sxs-lookup"><span data-stu-id="24d93-302">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="24d93-303">Используйте `X-FRAME-OPTIONS` для защиты сайта.</span><span class="sxs-lookup"><span data-stu-id="24d93-303">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="24d93-304">Чтобы уменьшить риск атак кликджекинга, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="24d93-304">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="24d93-305">Измените файл *nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="24d93-305">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="24d93-306">Добавьте строку: `add_header X-Frame-Options "SAMEORIGIN";`</span><span class="sxs-lookup"><span data-stu-id="24d93-306">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="24d93-307">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="24d93-307">Save the file.</span></span>
1. <span data-ttu-id="24d93-308">Перезапустите Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d93-308">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="24d93-309">Сканирование типа MIME</span><span class="sxs-lookup"><span data-stu-id="24d93-309">MIME-type sniffing</span></span>

<span data-ttu-id="24d93-310">Этот заголовок предотвращает MIME-сканирование ответов с указанным типом содержимого в большинстве браузеров, запрещая браузеру переопределять тип содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="24d93-310">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="24d93-311">Параметр `nosniff` означает, что если сервер определяет содержимое как `text/html`, то браузер будет обрабатывать его как `text/html`.</span><span class="sxs-lookup"><span data-stu-id="24d93-311">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="24d93-312">Измените файл *nginx.conf*.</span><span class="sxs-lookup"><span data-stu-id="24d93-312">Edit the *nginx.conf* file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="24d93-313">Добавьте строку: `add_header X-Content-Type-Options "nosniff";`</span><span class="sxs-lookup"><span data-stu-id="24d93-313">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="24d93-314">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="24d93-314">Save the file.</span></span>
1. <span data-ttu-id="24d93-315">Перезапустите Nginx.</span><span class="sxs-lookup"><span data-stu-id="24d93-315">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="24d93-316">Дополнительные предложения Nginx</span><span class="sxs-lookup"><span data-stu-id="24d93-316">Additional Nginx suggestions</span></span>

<span data-ttu-id="24d93-317">После обновления общей платформы на сервере перезапустите размещенные на нем приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="24d93-317">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24d93-318">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="24d93-318">Additional resources</span></span>

* [<span data-ttu-id="24d93-319">Необходимые компоненты для .NET Core в Linux</span><span class="sxs-lookup"><span data-stu-id="24d93-319">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="24d93-320">Nginx: двоичные выпуски: официальные пакеты Debian и Ubuntu</span><span class="sxs-lookup"><span data-stu-id="24d93-320">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="24d93-321">NGINX: использование перенаправленного заголовка</span><span class="sxs-lookup"><span data-stu-id="24d93-321">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
