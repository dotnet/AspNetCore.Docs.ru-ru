---
title: Реализации веб-сервера Kestrel в ASP.NET Core
author: rick-anderson
description: Общие сведения о Kestrel — кроссплатформенном веб-сервере для ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 54d7304430c2a77347b6e3279f638cf6d7add77e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589051"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="787d0-103">Реализации веб-сервера Kestrel в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="787d0-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="787d0-104">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra), [Крис Росс](https://github.com/Tratcher) (Chris Ross) и [Стивен Хальтер](https://twitter.com/halter73) (Stephen Halter)</span><span class="sxs-lookup"><span data-stu-id="787d0-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="787d0-105">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="787d0-106">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core и активируется в них.</span><span class="sxs-lookup"><span data-stu-id="787d0-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="787d0-107">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="787d0-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="787d0-108">HTTPS</span></span>
* <span data-ttu-id="787d0-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (за исключением macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="787d0-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="787d0-110">Непрозрачное обновление для поддержки [WebSocket](xref:fundamentals/websockets)</span><span class="sxs-lookup"><span data-stu-id="787d0-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="787d0-111">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="787d0-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="787d0-112">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="787d0-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="787d0-113">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="787d0-114">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="787d0-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="787d0-115">Начало работы</span><span class="sxs-lookup"><span data-stu-id="787d0-115">Get started</span></span>

<span data-ttu-id="787d0-116">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="787d0-117">В *Program.cs* метод <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="787d0-118">Дополнительные сведения о создании узла см. в разделах *Настройка узла* и *Параметры построителя по умолчанию* статьи <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="787d0-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="787d0-119">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="787d0-119">Additional resources</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="787d0-120">RFC 7230: синтаксис и маршрутизация сообщений (раздел 5.4: узел)</span><span class="sxs-lookup"><span data-stu-id="787d0-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="787d0-121">При использовании сокетов UNIX в Linux сокет не удаляется автоматически по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="787d0-122">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="787d0-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="787d0-123">Начиная с ASP.NET Core 5.0 транспорт Kestrel libuv считается устаревшим.</span><span class="sxs-lookup"><span data-stu-id="787d0-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="787d0-124">Транспорт libuv не получает обновления для включения поддержки новых платформ ОС, таких как Windows ARM64, и будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="787d0-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="787d0-125">Удалите все вызовы устаревшего метода <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> и используйте вместо него транспорт Socket Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="787d0-126">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="787d0-127">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="787d0-128">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="787d0-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="787d0-129">HTTPS</span></span>
* <span data-ttu-id="787d0-130">Непрозрачное обновление для поддержки [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="787d0-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="787d0-131">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="787d0-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="787d0-132">HTTP/2 (за исключением macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="787d0-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="787d0-133">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="787d0-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="787d0-134">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="787d0-135">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="787d0-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="787d0-136">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="787d0-136">HTTP/2 support</span></span>

<span data-ttu-id="787d0-137">Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) доступен для приложений ASP.NET Core, если выполнены следующие базовые требования:</span><span class="sxs-lookup"><span data-stu-id="787d0-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="787d0-138">Операционная система&dagger;:</span><span class="sxs-lookup"><span data-stu-id="787d0-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="787d0-139">Windows Server 2016 / Windows 10 или более поздних версий&Dagger;</span><span class="sxs-lookup"><span data-stu-id="787d0-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="787d0-140">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="787d0-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="787d0-141">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="787d0-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="787d0-142">Подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3).</span><span class="sxs-lookup"><span data-stu-id="787d0-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="787d0-143">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="787d0-144">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="787d0-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="787d0-145">&Dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="787d0-146">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="787d0-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="787d0-147">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="787d0-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="787d0-148">Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="787d0-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="787d0-149">Начиная с версии .NET Core 3.0 протокол HTTP/2 по умолчанию включен.</span><span class="sxs-lookup"><span data-stu-id="787d0-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="787d0-150">Дополнительные сведения о конфигурации см. в разделах [о параметрах Kestrel](#kestrel-options) и [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="787d0-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="787d0-151">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="787d0-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="787d0-152">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="787d0-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="787d0-153">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="787d0-154">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="787d0-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="787d0-156">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="787d0-158">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="787d0-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="787d0-159">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="787d0-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="787d0-160">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="787d0-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="787d0-161">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="787d0-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="787d0-162">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="787d0-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="787d0-163">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="787d0-163">A reverse proxy:</span></span>

* <span data-ttu-id="787d0-164">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="787d0-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="787d0-165">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="787d0-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="787d0-166">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="787d0-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="787d0-167">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="787d0-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="787d0-168">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="787d0-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="787d0-169">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="787d0-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="787d0-170">Kestrel в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="787d0-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="787d0-171">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="787d0-172">В *Program.cs* метод <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="787d0-173">Дополнительные сведения о создании узла см. в разделах *Настройка узла* и *Параметры построителя по умолчанию* статьи <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="787d0-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="787d0-174">Чтобы задать дополнительную конфигурацию после вызова `ConfigureWebHostDefaults`, используйте `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="787d0-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="787d0-175">Параметры Kestrel</span><span class="sxs-lookup"><span data-stu-id="787d0-175">Kestrel options</span></span>

<span data-ttu-id="787d0-176">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="787d0-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="787d0-177">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="787d0-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="787d0-178">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="787d0-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="787d0-179">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="787d0-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="787d0-180">В примерах, приведенных далее в этой статье, параметры Kestrel настраиваются в коде C#.</span><span class="sxs-lookup"><span data-stu-id="787d0-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="787d0-181">Параметры Kestrel можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="787d0-182">Например, [поставщик конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider) может загрузить конфигурацию Kestrel из файла *appsettings.json* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="787d0-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="787d0-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> и [конфигурацию конечных точек](#endpoint-configuration) можно настроить из поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="787d0-184">Оставшаяся конфигурация Kestrel должна настраиваться в коде C#.</span><span class="sxs-lookup"><span data-stu-id="787d0-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="787d0-185">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="787d0-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="787d0-186">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="787d0-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="787d0-187">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="787d0-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="787d0-188">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="787d0-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="787d0-189">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="787d0-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="787d0-190">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="787d0-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="787d0-191">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="787d0-192">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="787d0-193">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="787d0-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="787d0-194">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="787d0-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="787d0-195">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="787d0-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="787d0-196">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="787d0-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="787d0-197">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="787d0-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="787d0-198">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="787d0-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="787d0-199">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="787d0-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="787d0-200">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="787d0-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="787d0-201">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="787d0-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="787d0-202">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="787d0-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="787d0-203">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="787d0-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="787d0-204">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="787d0-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="787d0-205">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="787d0-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="787d0-206">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="787d0-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="787d0-207">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="787d0-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="787d0-208">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено, так как оно уже задается IIS.</span><span class="sxs-lookup"><span data-stu-id="787d0-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="787d0-209">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="787d0-210">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="787d0-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="787d0-211">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня; в течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="787d0-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="787d0-212">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="787d0-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="787d0-213">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="787d0-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="787d0-214">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="787d0-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="787d0-215">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="787d0-216">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="787d0-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="787d0-217">Переопределите ограничения минимальной скорости для каждого запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="787d0-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="787d0-218">Возможности <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, указанные в предыдущем примере, отсутствуют в `HttpContext.Features` для запросов HTTP/2, так как изменение ограничений скорости для каждого запроса не поддерживается для HTTP/2 из-за поддержки протокола мультиплексирования запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="787d0-219">Но возможности <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> все еще присутствуют в `HttpContext.Features` для запросов HTTP/2, так как ограничение скорости чтения может быть *полностью отключено* для отдельных запросов. Чтобы сделать это, задайте для параметра `IHttpMinRequestBodyDataRateFeature.MinDataRate` значение `null` (даже для запроса HTTP/2).</span><span class="sxs-lookup"><span data-stu-id="787d0-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="787d0-220">При попытке чтения свойства `IHttpMinRequestBodyDataRateFeature.MinDataRate` или при попытке задать для него значение, отличное от `null`, возникнет исключение `NotSupportedException` для запроса HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="787d0-221">Ограничения скорости на уровне сервера, которые настроены с помощью `KestrelServerOptions.Limits`, по-прежнему применяются к подключениям HTTP/1.x и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="787d0-222">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="787d0-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="787d0-223">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="787d0-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="787d0-224">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="787d0-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="787d0-225">Максимальное число потоков на подключение</span><span class="sxs-lookup"><span data-stu-id="787d0-225">Maximum streams per connection</span></span>

<span data-ttu-id="787d0-226">`Http2.MaxStreamsPerConnection` ограничивает количество параллельных потоков запросов для одного соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="787d0-227">Потоки сверх этого числа будут отклонены.</span><span class="sxs-lookup"><span data-stu-id="787d0-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="787d0-228">Значение по умолчанию — 100.</span><span class="sxs-lookup"><span data-stu-id="787d0-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="787d0-229">Размер таблицы заголовка</span><span class="sxs-lookup"><span data-stu-id="787d0-229">Header table size</span></span>

<span data-ttu-id="787d0-230">Декодер HPACK распаковывает заголовки HTTP для подключений HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="787d0-231">`Http2.HeaderTableSize` ограничивает размер таблицы сжатия заголовка, которую использует декодер HPACK.</span><span class="sxs-lookup"><span data-stu-id="787d0-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="787d0-232">Это значение указывается в октетах и должно быть больше нуля (0).</span><span class="sxs-lookup"><span data-stu-id="787d0-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="787d0-233">Значение по умолчанию — 4096.</span><span class="sxs-lookup"><span data-stu-id="787d0-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="787d0-234">Максимальный размер кадра</span><span class="sxs-lookup"><span data-stu-id="787d0-234">Maximum frame size</span></span>

<span data-ttu-id="787d0-235">`Http2.MaxFrameSize` указывает максимально допустимый размер полезных данных в кадре подключения HTTP/2, получаемых или отправляемых сервером.</span><span class="sxs-lookup"><span data-stu-id="787d0-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="787d0-236">Это значение указывается в октетах и должно находиться в пределах от 2^14 (16 384) до 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="787d0-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="787d0-237">Значение по умолчанию — 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="787d0-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="787d0-238">Максимальный размер запроса заголовка</span><span class="sxs-lookup"><span data-stu-id="787d0-238">Maximum request header size</span></span>

<span data-ttu-id="787d0-239">`Http2.MaxRequestHeaderFieldSize` указывает максимально допустимый размер значений заголовка запроса (в октетах).</span><span class="sxs-lookup"><span data-stu-id="787d0-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="787d0-240">Это ограничение применяется к имени и значению в их сжатых и несжатых представлениях.</span><span class="sxs-lookup"><span data-stu-id="787d0-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="787d0-241">Значение должно быть больше нуля.</span><span class="sxs-lookup"><span data-stu-id="787d0-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="787d0-242">Значение по умолчанию — 8 192.</span><span class="sxs-lookup"><span data-stu-id="787d0-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="787d0-243">Размер окна начального подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-243">Initial connection window size</span></span>

<span data-ttu-id="787d0-244">`Http2.InitialConnectionWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для всех запросов (потоков) на каждое соединение.</span><span class="sxs-lookup"><span data-stu-id="787d0-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="787d0-245">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="787d0-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="787d0-246">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="787d0-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="787d0-247">Значение по умолчанию — 128 КБ (131 072).</span><span class="sxs-lookup"><span data-stu-id="787d0-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="787d0-248">Размер окна начального потока</span><span class="sxs-lookup"><span data-stu-id="787d0-248">Initial stream window size</span></span>

<span data-ttu-id="787d0-249">`Http2.InitialStreamWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для каждого запроса (потока).</span><span class="sxs-lookup"><span data-stu-id="787d0-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="787d0-250">Размеры запросов также ограничиваются параметром `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="787d0-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="787d0-251">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="787d0-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="787d0-252">Значение по умолчанию — 96 КБ (98 304).</span><span class="sxs-lookup"><span data-stu-id="787d0-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="787d0-253">Трейлеры</span><span class="sxs-lookup"><span data-stu-id="787d0-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="787d0-254">Reset</span><span class="sxs-lookup"><span data-stu-id="787d0-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="787d0-255">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="787d0-255">Synchronous I/O</span></span>

<span data-ttu-id="787d0-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="787d0-257">Значение по умолчанию — `false`.</span><span class="sxs-lookup"><span data-stu-id="787d0-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="787d0-258">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="787d0-259">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="787d0-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="787d0-260">В примере ниже включаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="787d0-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="787d0-261">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="787d0-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="787d0-262">Конфигурация конечной точки</span><span class="sxs-lookup"><span data-stu-id="787d0-262">Endpoint configuration</span></span>

<span data-ttu-id="787d0-263">По умолчанию платформа ASP.NET Core привязана к:</span><span class="sxs-lookup"><span data-stu-id="787d0-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="787d0-264">`https://localhost:5001` (если присутствует локальный сертификат разработки)</span><span class="sxs-lookup"><span data-stu-id="787d0-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="787d0-265">Укажите URL-адреса с помощью следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="787d0-265">Specify URLs using the:</span></span>

* <span data-ttu-id="787d0-266">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="787d0-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="787d0-267">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="787d0-268">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="787d0-269">Метод расширения `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="787d0-270">Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="787d0-271">Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="787d0-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="787d0-272">Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="787d0-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="787d0-273">Сертификат разработки создается, когда:</span><span class="sxs-lookup"><span data-stu-id="787d0-273">A development certificate is created:</span></span>

* <span data-ttu-id="787d0-274">установлен [пакет SDK для .NET Core](/dotnet/core/sdk);</span><span class="sxs-lookup"><span data-stu-id="787d0-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="787d0-275">используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="787d0-276">В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="787d0-277">Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="787d0-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="787d0-278">Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="787d0-279">`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="787d0-280">Конфигурация `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="787d0-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="787d0-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="787d0-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="787d0-282">Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="787d0-283">Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="787d0-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="787d0-284">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="787d0-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="787d0-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="787d0-286">Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="787d0-287">Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="787d0-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="787d0-288">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="787d0-289">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="787d0-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="787d0-290">Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="787d0-291">Для Kestrel конфигурация должна быть ограничена разделом конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="787d0-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="787d0-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="787d0-293">Настройте Kestrel для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="787d0-294">Расширения `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="787d0-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="787d0-295">`UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="787d0-296">Создает исключение, если сертификат по умолчанию не настроен.</span><span class="sxs-lookup"><span data-stu-id="787d0-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="787d0-297">Параметры `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="787d0-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="787d0-298">`filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="787d0-299">`password` — это пароль для доступа к данным сертификата X.509.</span><span class="sxs-lookup"><span data-stu-id="787d0-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="787d0-300">`configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="787d0-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="787d0-301">Возвращает `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="787d0-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="787d0-302">`storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="787d0-303">`subject` — это имя субъекта для сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="787d0-304">`allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.</span><span class="sxs-lookup"><span data-stu-id="787d0-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="787d0-305">`location` — это расположение хранилища, из которого загружается сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="787d0-306">`serverCertificate` — это сертификат X.509.</span><span class="sxs-lookup"><span data-stu-id="787d0-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="787d0-307">В рабочей среде необходимо явно настроить HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="787d0-308">Как минимум необходимо указать сертификат по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="787d0-309">Поддерживаемые конфигурации, описанные далее:</span><span class="sxs-lookup"><span data-stu-id="787d0-309">Supported configurations described next:</span></span>

* <span data-ttu-id="787d0-310">Отсутствие конфигурации</span><span class="sxs-lookup"><span data-stu-id="787d0-310">No configuration</span></span>
* <span data-ttu-id="787d0-311">Замена сертификата по умолчанию из конфигурации</span><span class="sxs-lookup"><span data-stu-id="787d0-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="787d0-312">Изменение значений по умолчанию в коде</span><span class="sxs-lookup"><span data-stu-id="787d0-312">Change the defaults in code</span></span>

<span data-ttu-id="787d0-313">*Отсутствие конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-313">*No configuration*</span></span>

<span data-ttu-id="787d0-314">Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="787d0-315">*Замена сертификата по умолчанию из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="787d0-316">`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="787d0-317">Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="787d0-318">Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="787d0-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="787d0-319">В следующем примере *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="787d0-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="787d0-320">Установите для **AllowInvalid** значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).</span><span class="sxs-lookup"><span data-stu-id="787d0-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="787d0-321">Любая конечная точка HTTPS, которая не указывает сертификат (**HttpsDefaultCert** в следующем примере), будет использовать сертификат, определенный в разделе **Certificates** (Сертификаты) > **Default** (По умолчанию), или сертификат разработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="787d0-322">Вместо использования параметров **Path** и **Password** для узла сертификата можно указать сертификат с помощью полей хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="787d0-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="787d0-323">Например, сертификат из раздела **Сертификаты** > **По умолчанию** можно указать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="787d0-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="787d0-324">Примечания к схеме.</span><span class="sxs-lookup"><span data-stu-id="787d0-324">Schema notes:</span></span>

* <span data-ttu-id="787d0-325">Регистр букв в именах конечных точек не учитывается.</span><span class="sxs-lookup"><span data-stu-id="787d0-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="787d0-326">Например, `HTTPS` и `Https` являются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="787d0-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="787d0-327">Параметр `Url` является обязательным для каждой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="787d0-328">Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.</span><span class="sxs-lookup"><span data-stu-id="787d0-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="787d0-329">Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их.</span><span class="sxs-lookup"><span data-stu-id="787d0-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="787d0-330">Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="787d0-331">Раздел `Certificate` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="787d0-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="787d0-332">Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в предыдущих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="787d0-333">Если значений по умолчанию нет, сервер выдает исключение и не запускается.</span><span class="sxs-lookup"><span data-stu-id="787d0-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="787d0-334">Раздел `Certificate` поддерживает сертификаты **Path**&ndash;**Password** и **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="787d0-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="787d0-335">Таким образом, можно определить любое количество конечных точек, если это не приводит к конфликту портов.</span><span class="sxs-lookup"><span data-stu-id="787d0-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="787d0-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает `KestrelConfigurationLoader` с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="787d0-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="787d0-337">Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="787d0-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="787d0-338">Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.</span><span class="sxs-lookup"><span data-stu-id="787d0-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="787d0-339">Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом.</span><span class="sxs-lookup"><span data-stu-id="787d0-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="787d0-340">Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах.</span><span class="sxs-lookup"><span data-stu-id="787d0-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="787d0-341">Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="787d0-342">`KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте.</span><span class="sxs-lookup"><span data-stu-id="787d0-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="787d0-343">Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="787d0-344">*Изменение значений по умолчанию в коде*</span><span class="sxs-lookup"><span data-stu-id="787d0-344">*Change the defaults in code*</span></span>

<span data-ttu-id="787d0-345">Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии.</span><span class="sxs-lookup"><span data-stu-id="787d0-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="787d0-346">Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="787d0-347">*Поддержка Kestrel для SNI*</span><span class="sxs-lookup"><span data-stu-id="787d0-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="787d0-348">Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте.</span><span class="sxs-lookup"><span data-stu-id="787d0-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="787d0-349">Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="787d0-350">Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.</span><span class="sxs-lookup"><span data-stu-id="787d0-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="787d0-351">Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="787d0-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="787d0-352">Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="787d0-353">Поддержка SNI требует:</span><span class="sxs-lookup"><span data-stu-id="787d0-353">SNI support requires:</span></span>

* <span data-ttu-id="787d0-354">Запуск на целевой платформе `netcoreapp2.1` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="787d0-355">В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="787d0-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="787d0-356">`name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.</span><span class="sxs-lookup"><span data-stu-id="787d0-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="787d0-357">Все веб-сайты выполняются на одном и том же экземпляре Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="787d0-358">Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="787d0-359">Ведение журнала подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-359">Connection logging</span></span>

<span data-ttu-id="787d0-360">Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения.</span><span class="sxs-lookup"><span data-stu-id="787d0-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="787d0-361">Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами.</span><span class="sxs-lookup"><span data-stu-id="787d0-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="787d0-362">Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="787d0-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="787d0-363">Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="787d0-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="787d0-364">Привязка к TCP-сокету</span><span class="sxs-lookup"><span data-stu-id="787d0-364">Bind to a TCP socket</span></span>

<span data-ttu-id="787d0-365">Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:</span><span class="sxs-lookup"><span data-stu-id="787d0-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="787d0-366">В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="787d0-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="787d0-367">С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.</span><span class="sxs-lookup"><span data-stu-id="787d0-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="787d0-368">Привязка к сокету UNIX</span><span class="sxs-lookup"><span data-stu-id="787d0-368">Bind to a Unix socket</span></span>

<span data-ttu-id="787d0-369">Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, чтобы улучшить производительность Nginx, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="787d0-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="787d0-370">В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="787d0-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="787d0-371">`{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (как `kestrel-test.sock` в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="787d0-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="787d0-372">Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="787d0-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="787d0-373">Порт 0</span><span class="sxs-lookup"><span data-stu-id="787d0-373">Port 0</span></span>

<span data-ttu-id="787d0-374">Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту.</span><span class="sxs-lookup"><span data-stu-id="787d0-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="787d0-375">Следующий пример показывает, как определить, к какому порту фактически привязан Kestrel во время выполнения:</span><span class="sxs-lookup"><span data-stu-id="787d0-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="787d0-376">Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:</span><span class="sxs-lookup"><span data-stu-id="787d0-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="787d0-377">Ограничения</span><span class="sxs-lookup"><span data-stu-id="787d0-377">Limitations</span></span>

<span data-ttu-id="787d0-378">Настройте конечные точки с помощью следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="787d0-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="787d0-379">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="787d0-380">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-380">`urls` host configuration key</span></span>
* <span data-ttu-id="787d0-381">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="787d0-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="787d0-382">Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="787d0-383">Не забывайте о следующих ограничениях.</span><span class="sxs-lookup"><span data-stu-id="787d0-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="787d0-384">С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="787d0-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="787d0-385">Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="787d0-386">Конфигурация конечной точки IIS</span><span class="sxs-lookup"><span data-stu-id="787d0-386">IIS endpoint configuration</span></span>

<span data-ttu-id="787d0-387">При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="787d0-388">Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="787d0-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="787d0-389">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="787d0-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="787d0-390">Свойство `Protocols` устанавливает протоколы HTTP (`HttpProtocols`), разрешенные для конечной точки подключения или для сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="787d0-391">Значение свойства `Protocols` должно входить в перечисление `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="787d0-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="787d0-392">Значение перечисления `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="787d0-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="787d0-393">Допустимый протокол подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="787d0-394">Только HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-394">HTTP/1.1 only.</span></span> <span data-ttu-id="787d0-395">Можно использовать с протоколом TLS или без него.</span><span class="sxs-lookup"><span data-stu-id="787d0-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="787d0-396">Только HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-396">HTTP/2 only.</span></span> <span data-ttu-id="787d0-397">Может использоваться без TLS только в том случае, если клиент поддерживает [режим предварительного знания](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="787d0-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="787d0-398">HTTP/1.1 и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="787d0-399">Для использования HTTP/2 требуется, чтобы клиент выбрал HTTP/2 при подтверждении [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); в противном случае используется подключение по умолчанию HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="787d0-400">Значение `ListenOptions.Protocols` по умолчанию для любой конечной точки равно `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="787d0-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="787d0-401">Ограничения TLS для HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="787d0-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="787d0-402">TSL 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="787d0-403">Повторное согласование отключено.</span><span class="sxs-lookup"><span data-stu-id="787d0-403">Renegotiation disabled</span></span>
* <span data-ttu-id="787d0-404">Сжатие отключено.</span><span class="sxs-lookup"><span data-stu-id="787d0-404">Compression disabled</span></span>
* <span data-ttu-id="787d0-405">Минимальные размеры обмена временными ключами:</span><span class="sxs-lookup"><span data-stu-id="787d0-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="787d0-406">эллиптическая кривая Диффи—Хелмана (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: не менее 224 бит;</span><span class="sxs-lookup"><span data-stu-id="787d0-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="787d0-407">конечное поле Диффи—Хелмана (DHE) &lbrack;`TLS12`&rbrack;: не менее 2048 бит.</span><span class="sxs-lookup"><span data-stu-id="787d0-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="787d0-408">Набор шифров не запрещен.</span><span class="sxs-lookup"><span data-stu-id="787d0-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="787d0-409">По умолчанию поддерживается `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; с эллиптической кривой P-256 &lbrack;`FIPS186`&rbrack;.</span><span class="sxs-lookup"><span data-stu-id="787d0-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="787d0-410">Следующий пример разрешает подключения HTTP/1.1 и HTTP/2 через порт 8000.</span><span class="sxs-lookup"><span data-stu-id="787d0-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="787d0-411">Эти подключения шифруются по протоколу TLS с использованием предоставленного сертификата:</span><span class="sxs-lookup"><span data-stu-id="787d0-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="787d0-412">При необходимости, используйте ПО промежуточного слоя подключения для фильтрации подтверждений TLS для каждого соединения по конкретным шифрам.</span><span class="sxs-lookup"><span data-stu-id="787d0-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="787d0-413">Следующий пример вызывает <xref:System.NotSupportedException> для любого алгоритма шифрования, который не поддерживается приложением.</span><span class="sxs-lookup"><span data-stu-id="787d0-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="787d0-414">Также можно определить и сравнить [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) со списком приемлемых наборов шифров.</span><span class="sxs-lookup"><span data-stu-id="787d0-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="787d0-415">При использовании алгоритма шифрования [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) шифрование не используется.</span><span class="sxs-lookup"><span data-stu-id="787d0-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="787d0-416">Фильтрацию соединений также можно настроить с помощью лямбды <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="787d0-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="787d0-417">В Linux для фильтрации подтверждений TLS по каждому соединению можно использовать <xref:System.Net.Security.CipherSuitesPolicy>:</span><span class="sxs-lookup"><span data-stu-id="787d0-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="787d0-418">*Выбор протокола из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="787d0-419">`CreateDefaultBuilder` по умолчанию вызывает `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="787d0-420">В следующем примере *appsettings.json* для всех конечных точек устанавливается протокол подключения по умолчанию HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="787d0-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="787d0-421">В следующем примере *appsettings.json* для отдельной конечной точки устанавливается протокол подключения HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="787d0-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="787d0-422">Указанные в коде протоколы переопределяют значения, заданные в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="787d0-423">Префиксы URL-адресов</span><span class="sxs-lookup"><span data-stu-id="787d0-423">URL prefixes</span></span>

<span data-ttu-id="787d0-424">Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.</span><span class="sxs-lookup"><span data-stu-id="787d0-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="787d0-425">Допустимы только префиксы URL-адресов HTTP.</span><span class="sxs-lookup"><span data-stu-id="787d0-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="787d0-426">Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="787d0-427">IPv4-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="787d0-428">`0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.</span><span class="sxs-lookup"><span data-stu-id="787d0-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="787d0-429">IPv6-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="787d0-430">`[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="787d0-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="787d0-431">Имя узла с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="787d0-432">Имена узлов, `*` и `+`, не являются особыми.</span><span class="sxs-lookup"><span data-stu-id="787d0-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="787d0-433">Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="787d0-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="787d0-434">Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер, такой как IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="787d0-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="787d0-435">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="787d0-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="787d0-436">Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="787d0-437">Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="787d0-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="787d0-438">Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается.</span><span class="sxs-lookup"><span data-stu-id="787d0-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="787d0-439">Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.</span><span class="sxs-lookup"><span data-stu-id="787d0-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="787d0-440">Фильтрация узлов</span><span class="sxs-lookup"><span data-stu-id="787d0-440">Host filtering</span></span>

<span data-ttu-id="787d0-441">Хотя Kestrel поддерживает конфигурации с использованием префиксов, такие как `http://example.com:5000`, Kestrel не учитывает имя узла.</span><span class="sxs-lookup"><span data-stu-id="787d0-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="787d0-442">Узел `localhost` является особым случаем, используемым для привязки к адресам замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="787d0-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="787d0-443">Любой узел, отличный от явного IP-адреса, привязывается ко всем общедоступным IP-адресам.</span><span class="sxs-lookup"><span data-stu-id="787d0-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="787d0-444">Заголовки `Host` не проверяются.</span><span class="sxs-lookup"><span data-stu-id="787d0-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="787d0-445">В качестве обходного решения используйте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="787d0-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="787d0-446">ПО промежуточного слоя фильтрации узлов предоставляется пакетом [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), который неявно предоставляется для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="787d0-447">ПО промежуточного слоя добавляется в <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который вызывает <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="787d0-448">ПО промежуточного слоя фильтрации узлов отключено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="787d0-449">Чтобы включить ПО промежуточного слоя, определите ключ `AllowedHosts` в *appsettings.json* /*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="787d0-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="787d0-450">Значение представляет собой разделенный точками с запятой список имен узлов без номеров портов:</span><span class="sxs-lookup"><span data-stu-id="787d0-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="787d0-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="787d0-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="787d0-452">[ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer) имеет также параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="787d0-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="787d0-453">ПО промежуточного слоя перенаправления заголовков и ПО промежуточного слоя фильтрации узлов обладают сходными возможностями для различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="787d0-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="787d0-454">Параметр `AllowedHosts` с ПО промежуточного слоя перенаправления заголовков подходит для случаев, когда заголовок `Host` не сохраняется при переадресации запросов с помощью обратного прокси-сервера или подсистемы балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="787d0-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="787d0-455">Параметр `AllowedHosts` с ПО промежуточного слоя фильтрации узлов подходит для случаев, когда Kestrel используется в качестве общедоступного пограничного сервера или если заголовок `Host` пересылается напрямую.</span><span class="sxs-lookup"><span data-stu-id="787d0-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="787d0-456">Дополнительные сведения о ПО промежуточного слоя перенаправления заголовков см. в статье <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="787d0-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="787d0-457">Конфигурация транспорта Libuv</span><span class="sxs-lookup"><span data-stu-id="787d0-457">Libuv transport configuration</span></span>

<span data-ttu-id="787d0-458">Для проектов, где требуется использовать Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span><span class="sxs-lookup"><span data-stu-id="787d0-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="787d0-459">Добавьте зависимость для пакета [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="787d0-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="787d0-460">Вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> для `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="787d0-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

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
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="787d0-461">Очистка запросов HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="787d0-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="787d0-462">Открытие HTTP-соединений занимает много времени.</span><span class="sxs-lookup"><span data-stu-id="787d0-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="787d0-463">Для протокола HTTPS это также требует больших ресурсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="787d0-464">Поэтому Kestrel пытается повторно использовать подключения по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="787d0-465">Чтобы разрешить повторное использование соединения, текст запроса должен быть полностью использован.</span><span class="sxs-lookup"><span data-stu-id="787d0-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="787d0-466">Приложение не всегда использует текст запроса, например запросы `POST`, в которых сервер возвращает ответ перенаправления или 404.</span><span class="sxs-lookup"><span data-stu-id="787d0-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="787d0-467">В случае перенаправления `POST`:</span><span class="sxs-lookup"><span data-stu-id="787d0-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="787d0-468">Возможно, клиент уже отправил часть данных `POST`.</span><span class="sxs-lookup"><span data-stu-id="787d0-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="787d0-469">Сервер записывает ответ 301.</span><span class="sxs-lookup"><span data-stu-id="787d0-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="787d0-470">Соединение нельзя использовать для нового запроса, пока не будут полностью прочитаны данные `POST` из предыдущего текста запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="787d0-471">Kestrel пытается очистить текст запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="787d0-472">Очистка текста запроса означает чтение и отмену данных без их обработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="787d0-473">Процесс очистки позволяет найти баланс между возможностью повторного использования соединения и временем, которое требуется для очистки оставшихся данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="787d0-474">Время ожидания очистки составляет 5 секунд. Этот параметр нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="787d0-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="787d0-475">Если все данные, указанные в заголовке `Content-Length` или `Transfer-Encoding`, не были считаны до истечения времени ожидания, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="787d0-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="787d0-476">Иногда может потребоваться немедленно завершить запрос до или после записи ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="787d0-477">Например, клиенты могут иметь ограничения на данные, поэтому ограничение передаваемых данных может иметь приоритет.</span><span class="sxs-lookup"><span data-stu-id="787d0-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="787d0-478">В таких случаях для завершения запроса вызовите [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) из контроллера, страницы Razor или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="787d0-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="787d0-479">Вызов `Abort` имеет определенные недостатки.</span><span class="sxs-lookup"><span data-stu-id="787d0-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="787d0-480">Создание новых подключений может выполняться очень медленно и требовать много ресурсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="787d0-481">Нет никакой гарантии, что клиент прочитал ответ перед закрытием соединения.</span><span class="sxs-lookup"><span data-stu-id="787d0-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="787d0-482">Вызов `Abort` следует использовать редко и только для серьезных, а не распространенных ошибок.</span><span class="sxs-lookup"><span data-stu-id="787d0-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="787d0-483">Вызывайте `Abort`, только когда нужно решить конкретную проблему.</span><span class="sxs-lookup"><span data-stu-id="787d0-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="787d0-484">Например, вызовите `Abort`, если вредоносные клиенты пытаются выполнить операцию `POST` с данными или если в клиентском коде есть ошибка, вызывающая большие или многочисленные запросы.</span><span class="sxs-lookup"><span data-stu-id="787d0-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="787d0-485">Не вызывайте `Abort` для распространенных ошибок, таких как HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="787d0-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="787d0-486">Вызов [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) перед вызовом `Abort` гарантирует, что сервер завершит запись ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="787d0-487">Однако поведение клиента не предсказуемо. Он может не считать ответ, прежде чем подключение будет прервано.</span><span class="sxs-lookup"><span data-stu-id="787d0-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="787d0-488">Этот процесс отличается для HTTP/2, так как протокол поддерживает прерывание отдельных потоков запросов без закрытия соединения.</span><span class="sxs-lookup"><span data-stu-id="787d0-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="787d0-489">5-секундное время ожидания очистки не применяется.</span><span class="sxs-lookup"><span data-stu-id="787d0-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="787d0-490">Если после завершения ответа сервер содержит непрочтенные данные текста запроса, он отправляет кадр HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="787d0-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="787d0-491">Дополнительные кадры данных текста запроса игнорируются.</span><span class="sxs-lookup"><span data-stu-id="787d0-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="787d0-492">По возможности клиентам лучше использовать заголовок запроса [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) и дожидаться ответа сервера перед началом отправки текста запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="787d0-493">Это дает клиенту возможность проверить ответ и прервать операцию перед отправкой ненужных данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="787d0-494">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="787d0-494">Additional resources</span></span>

* <span data-ttu-id="787d0-495">При использовании сокетов UNIX в Linux сокет не удаляется автоматически по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="787d0-496">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="787d0-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="787d0-497">RFC 7230: синтаксис и маршрутизация сообщений (раздел 5.4: узел)</span><span class="sxs-lookup"><span data-stu-id="787d0-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="787d0-498">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="787d0-499">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="787d0-500">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="787d0-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="787d0-501">HTTPS</span></span>
* <span data-ttu-id="787d0-502">Непрозрачное обновление для поддержки [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="787d0-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="787d0-503">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="787d0-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="787d0-504">HTTP/2 (за исключением macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="787d0-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="787d0-505">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="787d0-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="787d0-506">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="787d0-507">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="787d0-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="787d0-508">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="787d0-508">HTTP/2 support</span></span>

<span data-ttu-id="787d0-509">Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) доступен для приложений ASP.NET Core, если выполнены следующие базовые требования:</span><span class="sxs-lookup"><span data-stu-id="787d0-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="787d0-510">Операционная система&dagger;:</span><span class="sxs-lookup"><span data-stu-id="787d0-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="787d0-511">Windows Server 2016 / Windows 10 или более поздних версий&Dagger;</span><span class="sxs-lookup"><span data-stu-id="787d0-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="787d0-512">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="787d0-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="787d0-513">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="787d0-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="787d0-514">Подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3).</span><span class="sxs-lookup"><span data-stu-id="787d0-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="787d0-515">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="787d0-516">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="787d0-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="787d0-517">&Dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="787d0-518">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="787d0-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="787d0-519">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="787d0-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="787d0-520">Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="787d0-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="787d0-521">По умолчанию протокол HTTP/2 отключен.</span><span class="sxs-lookup"><span data-stu-id="787d0-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="787d0-522">Дополнительные сведения о конфигурации см. в разделах [о параметрах Kestrel](#kestrel-options) и [ListenOptions.Protocols](#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="787d0-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="787d0-523">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="787d0-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="787d0-524">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="787d0-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="787d0-525">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="787d0-526">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="787d0-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="787d0-528">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="787d0-530">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="787d0-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="787d0-531">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="787d0-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="787d0-532">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="787d0-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="787d0-533">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="787d0-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="787d0-534">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="787d0-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="787d0-535">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="787d0-535">A reverse proxy:</span></span>

* <span data-ttu-id="787d0-536">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="787d0-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="787d0-537">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="787d0-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="787d0-538">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="787d0-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="787d0-539">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="787d0-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="787d0-540">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="787d0-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="787d0-541">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="787d0-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="787d0-542">Условия использования Kestrel в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="787d0-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="787d0-543">Пакет [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="787d0-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="787d0-544">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="787d0-545">В файле *Program.cs* код шаблона вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который в фоновом режиме вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>.</span><span class="sxs-lookup"><span data-stu-id="787d0-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="787d0-546">Дополнительные сведения о `CreateDefaultBuilder` и построении узла, см. в разделе *Настройка узла*, разделы <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="787d0-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="787d0-547">Чтобы задать дополнительную конфигурацию после вызова `CreateDefaultBuilder`, используйте `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="787d0-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="787d0-548">Если приложение не вызывает `CreateDefaultBuilder`, чтобы настроить узел, вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **перед** вызовом `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="787d0-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="787d0-549">Параметры Kestrel</span><span class="sxs-lookup"><span data-stu-id="787d0-549">Kestrel options</span></span>

<span data-ttu-id="787d0-550">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="787d0-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="787d0-551">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="787d0-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="787d0-552">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="787d0-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="787d0-553">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="787d0-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="787d0-554">Параметры Kestrel, которые настраиваются в C# коде в следующих примерах, можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="787d0-555">Например, поставщик конфигурации файла может загрузить конфигурацию Kestrel из файла *appsettings.json* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="787d0-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="787d0-556">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="787d0-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="787d0-557">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="787d0-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="787d0-558">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="787d0-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="787d0-559">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="787d0-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="787d0-560">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="787d0-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="787d0-561">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="787d0-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="787d0-562">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="787d0-563">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="787d0-564">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="787d0-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="787d0-565">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="787d0-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="787d0-566">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="787d0-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="787d0-567">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="787d0-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="787d0-568">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="787d0-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="787d0-569">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="787d0-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="787d0-570">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="787d0-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="787d0-571">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="787d0-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="787d0-572">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="787d0-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="787d0-573">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="787d0-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="787d0-574">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="787d0-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="787d0-575">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="787d0-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="787d0-576">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="787d0-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="787d0-577">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="787d0-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="787d0-578">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="787d0-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="787d0-579">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено, так как оно уже задается IIS.</span><span class="sxs-lookup"><span data-stu-id="787d0-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="787d0-580">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="787d0-581">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="787d0-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="787d0-582">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня; в течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="787d0-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="787d0-583">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="787d0-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="787d0-584">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="787d0-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="787d0-585">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="787d0-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="787d0-586">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="787d0-587">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="787d0-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="787d0-588">Переопределите ограничения минимальной скорости для каждого запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="787d0-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="787d0-589">В `HttpContext.Features` для запросов HTTP/2 отсутствуют возможности настройки скорости, указанные в предыдущем примере, так как изменение ограничений скорости для каждого запроса не поддерживается для HTTP/2 из-за поддержки протокола мультиплексирования запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="787d0-590">Ограничения скорости на уровне сервера, которые настроены с помощью `KestrelServerOptions.Limits`, по-прежнему применяются к подключениям HTTP/1.x и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="787d0-591">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="787d0-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="787d0-592">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="787d0-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="787d0-593">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="787d0-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="787d0-594">Максимальное число потоков на подключение</span><span class="sxs-lookup"><span data-stu-id="787d0-594">Maximum streams per connection</span></span>

<span data-ttu-id="787d0-595">`Http2.MaxStreamsPerConnection` ограничивает количество параллельных потоков запросов для одного соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="787d0-596">Потоки сверх этого числа будут отклонены.</span><span class="sxs-lookup"><span data-stu-id="787d0-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="787d0-597">Значение по умолчанию — 100.</span><span class="sxs-lookup"><span data-stu-id="787d0-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="787d0-598">Размер таблицы заголовка</span><span class="sxs-lookup"><span data-stu-id="787d0-598">Header table size</span></span>

<span data-ttu-id="787d0-599">Декодер HPACK распаковывает заголовки HTTP для подключений HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="787d0-600">`Http2.HeaderTableSize` ограничивает размер таблицы сжатия заголовка, которую использует декодер HPACK.</span><span class="sxs-lookup"><span data-stu-id="787d0-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="787d0-601">Это значение указывается в октетах и должно быть больше нуля (0).</span><span class="sxs-lookup"><span data-stu-id="787d0-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="787d0-602">Значение по умолчанию — 4096.</span><span class="sxs-lookup"><span data-stu-id="787d0-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="787d0-603">Максимальный размер кадра</span><span class="sxs-lookup"><span data-stu-id="787d0-603">Maximum frame size</span></span>

<span data-ttu-id="787d0-604">`Http2.MaxFrameSize` указывает максимальный размер полезных данных в получаемом кадре подключения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="787d0-605">Это значение указывается в октетах и должно находиться в пределах от 2^14 (16 384) до 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="787d0-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="787d0-606">Значение по умолчанию — 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="787d0-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="787d0-607">Максимальный размер запроса заголовка</span><span class="sxs-lookup"><span data-stu-id="787d0-607">Maximum request header size</span></span>

<span data-ttu-id="787d0-608">`Http2.MaxRequestHeaderFieldSize` указывает максимально допустимый размер значений заголовка запроса (в октетах).</span><span class="sxs-lookup"><span data-stu-id="787d0-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="787d0-609">Это ограничение применяется к имени и значению в их сжатых и несжатых представлениях.</span><span class="sxs-lookup"><span data-stu-id="787d0-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="787d0-610">Значение должно быть больше нуля.</span><span class="sxs-lookup"><span data-stu-id="787d0-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="787d0-611">Значение по умолчанию — 8 192.</span><span class="sxs-lookup"><span data-stu-id="787d0-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="787d0-612">Размер окна начального подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-612">Initial connection window size</span></span>

<span data-ttu-id="787d0-613">`Http2.InitialConnectionWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для всех запросов (потоков) на каждое соединение.</span><span class="sxs-lookup"><span data-stu-id="787d0-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="787d0-614">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="787d0-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="787d0-615">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="787d0-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="787d0-616">Значение по умолчанию — 128 КБ (131 072).</span><span class="sxs-lookup"><span data-stu-id="787d0-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="787d0-617">Размер окна начального потока</span><span class="sxs-lookup"><span data-stu-id="787d0-617">Initial stream window size</span></span>

<span data-ttu-id="787d0-618">`Http2.InitialStreamWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для каждого запроса (потока).</span><span class="sxs-lookup"><span data-stu-id="787d0-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="787d0-619">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="787d0-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="787d0-620">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="787d0-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="787d0-621">Значение по умолчанию — 96 КБ (98 304).</span><span class="sxs-lookup"><span data-stu-id="787d0-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="787d0-622">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="787d0-622">Synchronous I/O</span></span>

<span data-ttu-id="787d0-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="787d0-624">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="787d0-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="787d0-625">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="787d0-626">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="787d0-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="787d0-627">В примере ниже включаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="787d0-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="787d0-628">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="787d0-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="787d0-629">Конфигурация конечной точки</span><span class="sxs-lookup"><span data-stu-id="787d0-629">Endpoint configuration</span></span>

<span data-ttu-id="787d0-630">По умолчанию платформа ASP.NET Core привязана к:</span><span class="sxs-lookup"><span data-stu-id="787d0-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="787d0-631">`https://localhost:5001` (если присутствует локальный сертификат разработки)</span><span class="sxs-lookup"><span data-stu-id="787d0-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="787d0-632">Укажите URL-адреса с помощью следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="787d0-632">Specify URLs using the:</span></span>

* <span data-ttu-id="787d0-633">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="787d0-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="787d0-634">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="787d0-635">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="787d0-636">Метод расширения `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="787d0-637">Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="787d0-638">Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="787d0-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="787d0-639">Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="787d0-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="787d0-640">Сертификат разработки создается, когда:</span><span class="sxs-lookup"><span data-stu-id="787d0-640">A development certificate is created:</span></span>

* <span data-ttu-id="787d0-641">установлен [пакет SDK для .NET Core](/dotnet/core/sdk);</span><span class="sxs-lookup"><span data-stu-id="787d0-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="787d0-642">используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="787d0-643">В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="787d0-644">Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="787d0-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="787d0-645">Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="787d0-646">`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="787d0-647">Конфигурация `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="787d0-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="787d0-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="787d0-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="787d0-649">Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="787d0-650">Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="787d0-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="787d0-651">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="787d0-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="787d0-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="787d0-653">Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="787d0-654">Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="787d0-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="787d0-655">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="787d0-656">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="787d0-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="787d0-657">Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="787d0-658">Для Kestrel конфигурация должна быть ограничена разделом конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="787d0-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="787d0-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="787d0-660">Настройте Kestrel для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="787d0-661">Расширения `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="787d0-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="787d0-662">`UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="787d0-663">Создает исключение, если сертификат по умолчанию не настроен.</span><span class="sxs-lookup"><span data-stu-id="787d0-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="787d0-664">Параметры `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="787d0-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="787d0-665">`filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="787d0-666">`password` — это пароль для доступа к данным сертификата X.509.</span><span class="sxs-lookup"><span data-stu-id="787d0-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="787d0-667">`configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="787d0-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="787d0-668">Возвращает `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="787d0-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="787d0-669">`storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="787d0-670">`subject` — это имя субъекта для сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="787d0-671">`allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.</span><span class="sxs-lookup"><span data-stu-id="787d0-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="787d0-672">`location` — это расположение хранилища, из которого загружается сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="787d0-673">`serverCertificate` — это сертификат X.509.</span><span class="sxs-lookup"><span data-stu-id="787d0-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="787d0-674">В рабочей среде необходимо явно настроить HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="787d0-675">Как минимум необходимо указать сертификат по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="787d0-676">Поддерживаемые конфигурации, описанные далее:</span><span class="sxs-lookup"><span data-stu-id="787d0-676">Supported configurations described next:</span></span>

* <span data-ttu-id="787d0-677">Отсутствие конфигурации</span><span class="sxs-lookup"><span data-stu-id="787d0-677">No configuration</span></span>
* <span data-ttu-id="787d0-678">Замена сертификата по умолчанию из конфигурации</span><span class="sxs-lookup"><span data-stu-id="787d0-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="787d0-679">Изменение значений по умолчанию в коде</span><span class="sxs-lookup"><span data-stu-id="787d0-679">Change the defaults in code</span></span>

<span data-ttu-id="787d0-680">*Отсутствие конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-680">*No configuration*</span></span>

<span data-ttu-id="787d0-681">Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="787d0-682">*Замена сертификата по умолчанию из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="787d0-683">`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="787d0-684">Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="787d0-685">Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="787d0-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="787d0-686">В следующем примере *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="787d0-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="787d0-687">Установите для **AllowInvalid** значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).</span><span class="sxs-lookup"><span data-stu-id="787d0-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="787d0-688">Любая конечная точка HTTPS, которая не указывает сертификат (**HttpsDefaultCert** в следующем примере), будет использовать сертификат, определенный в разделе **Certificates** (Сертификаты) > **Default** (По умолчанию), или сертификат разработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="787d0-689">Вместо использования параметров **Path** и **Password** для узла сертификата можно указать сертификат с помощью полей хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="787d0-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="787d0-690">Например, сертификат из раздела **Сертификаты** > **По умолчанию** можно указать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="787d0-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="787d0-691">Примечания к схеме.</span><span class="sxs-lookup"><span data-stu-id="787d0-691">Schema notes:</span></span>

* <span data-ttu-id="787d0-692">Регистр букв в именах конечных точек не учитывается.</span><span class="sxs-lookup"><span data-stu-id="787d0-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="787d0-693">Например, `HTTPS` и `Https` являются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="787d0-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="787d0-694">Параметр `Url` является обязательным для каждой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="787d0-695">Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.</span><span class="sxs-lookup"><span data-stu-id="787d0-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="787d0-696">Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их.</span><span class="sxs-lookup"><span data-stu-id="787d0-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="787d0-697">Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="787d0-698">Раздел `Certificate` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="787d0-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="787d0-699">Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в предыдущих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="787d0-700">Если значений по умолчанию нет, сервер выдает исключение и не запускается.</span><span class="sxs-lookup"><span data-stu-id="787d0-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="787d0-701">Раздел `Certificate` поддерживает сертификаты **Path**&ndash;**Password** и **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="787d0-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="787d0-702">Таким образом, можно определить любое количество конечных точек, если это не приводит к конфликту портов.</span><span class="sxs-lookup"><span data-stu-id="787d0-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="787d0-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает `KestrelConfigurationLoader` с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="787d0-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="787d0-704">Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="787d0-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="787d0-705">Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.</span><span class="sxs-lookup"><span data-stu-id="787d0-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="787d0-706">Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом.</span><span class="sxs-lookup"><span data-stu-id="787d0-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="787d0-707">Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах.</span><span class="sxs-lookup"><span data-stu-id="787d0-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="787d0-708">Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="787d0-709">`KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте.</span><span class="sxs-lookup"><span data-stu-id="787d0-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="787d0-710">Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="787d0-711">*Изменение значений по умолчанию в коде*</span><span class="sxs-lookup"><span data-stu-id="787d0-711">*Change the defaults in code*</span></span>

<span data-ttu-id="787d0-712">Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии.</span><span class="sxs-lookup"><span data-stu-id="787d0-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="787d0-713">Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="787d0-714">*Поддержка Kestrel для SNI*</span><span class="sxs-lookup"><span data-stu-id="787d0-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="787d0-715">Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте.</span><span class="sxs-lookup"><span data-stu-id="787d0-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="787d0-716">Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="787d0-717">Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.</span><span class="sxs-lookup"><span data-stu-id="787d0-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="787d0-718">Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="787d0-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="787d0-719">Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="787d0-720">Поддержка SNI требует:</span><span class="sxs-lookup"><span data-stu-id="787d0-720">SNI support requires:</span></span>

* <span data-ttu-id="787d0-721">Запуск на целевой платформе `netcoreapp2.1` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="787d0-722">В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="787d0-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="787d0-723">`name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.</span><span class="sxs-lookup"><span data-stu-id="787d0-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="787d0-724">Все веб-сайты выполняются на одном и том же экземпляре Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="787d0-725">Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="787d0-726">Ведение журнала подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-726">Connection logging</span></span>

<span data-ttu-id="787d0-727">Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения.</span><span class="sxs-lookup"><span data-stu-id="787d0-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="787d0-728">Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами.</span><span class="sxs-lookup"><span data-stu-id="787d0-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="787d0-729">Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="787d0-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="787d0-730">Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="787d0-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="787d0-731">Привязка к TCP-сокету</span><span class="sxs-lookup"><span data-stu-id="787d0-731">Bind to a TCP socket</span></span>

<span data-ttu-id="787d0-732">Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:</span><span class="sxs-lookup"><span data-stu-id="787d0-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="787d0-733">В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="787d0-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="787d0-734">С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.</span><span class="sxs-lookup"><span data-stu-id="787d0-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="787d0-735">Привязка к сокету UNIX</span><span class="sxs-lookup"><span data-stu-id="787d0-735">Bind to a Unix socket</span></span>

<span data-ttu-id="787d0-736">Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, чтобы улучшить производительность Nginx, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="787d0-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="787d0-737">В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="787d0-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="787d0-738">`{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (как `kestrel-test.sock` в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="787d0-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="787d0-739">Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="787d0-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="787d0-740">Порт 0</span><span class="sxs-lookup"><span data-stu-id="787d0-740">Port 0</span></span>

<span data-ttu-id="787d0-741">Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту.</span><span class="sxs-lookup"><span data-stu-id="787d0-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="787d0-742">Следующий пример показывает, как определить, к какому порту фактически привязан Kestrel во время выполнения:</span><span class="sxs-lookup"><span data-stu-id="787d0-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="787d0-743">Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:</span><span class="sxs-lookup"><span data-stu-id="787d0-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="787d0-744">Ограничения</span><span class="sxs-lookup"><span data-stu-id="787d0-744">Limitations</span></span>

<span data-ttu-id="787d0-745">Настройте конечные точки с помощью следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="787d0-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="787d0-746">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="787d0-747">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-747">`urls` host configuration key</span></span>
* <span data-ttu-id="787d0-748">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="787d0-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="787d0-749">Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="787d0-750">Не забывайте о следующих ограничениях.</span><span class="sxs-lookup"><span data-stu-id="787d0-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="787d0-751">С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="787d0-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="787d0-752">Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="787d0-753">Конфигурация конечной точки IIS</span><span class="sxs-lookup"><span data-stu-id="787d0-753">IIS endpoint configuration</span></span>

<span data-ttu-id="787d0-754">При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="787d0-755">Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="787d0-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="787d0-756">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="787d0-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="787d0-757">Свойство `Protocols` устанавливает протоколы HTTP (`HttpProtocols`), разрешенные для конечной точки подключения или для сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="787d0-758">Значение свойства `Protocols` должно входить в перечисление `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="787d0-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="787d0-759">Значение перечисления `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="787d0-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="787d0-760">Допустимый протокол подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="787d0-761">Только HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-761">HTTP/1.1 only.</span></span> <span data-ttu-id="787d0-762">Можно использовать с протоколом TLS или без него.</span><span class="sxs-lookup"><span data-stu-id="787d0-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="787d0-763">Только HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-763">HTTP/2 only.</span></span> <span data-ttu-id="787d0-764">Может использоваться без TLS только в том случае, если клиент поддерживает [режим предварительного знания](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="787d0-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="787d0-765">HTTP/1.1 и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="787d0-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="787d0-766">Для использования HTTP/2 требуется протокол TLS и подключение с [согласованием протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); при их отсутствии используется подключение по умолчанию HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="787d0-767">По умолчанию используется протокол HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="787d0-768">Ограничения TLS для HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="787d0-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="787d0-769">TSL 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="787d0-770">Повторное согласование отключено.</span><span class="sxs-lookup"><span data-stu-id="787d0-770">Renegotiation disabled</span></span>
* <span data-ttu-id="787d0-771">Сжатие отключено.</span><span class="sxs-lookup"><span data-stu-id="787d0-771">Compression disabled</span></span>
* <span data-ttu-id="787d0-772">Минимальные размеры обмена временными ключами:</span><span class="sxs-lookup"><span data-stu-id="787d0-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="787d0-773">эллиптическая кривая Диффи—Хелмана (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: не менее 224 бит;</span><span class="sxs-lookup"><span data-stu-id="787d0-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="787d0-774">конечное поле Диффи—Хелмана (DHE) &lbrack;`TLS12`&rbrack;: не менее 2048 бит.</span><span class="sxs-lookup"><span data-stu-id="787d0-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="787d0-775">Набор шифров не блокируется</span><span class="sxs-lookup"><span data-stu-id="787d0-775">Cipher suite not blocked</span></span>

<span data-ttu-id="787d0-776">По умолчанию поддерживается `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; с эллиптической кривой P-256 &lbrack;`FIPS186`&rbrack;.</span><span class="sxs-lookup"><span data-stu-id="787d0-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="787d0-777">Следующий пример разрешает подключения HTTP/1.1 и HTTP/2 через порт 8000.</span><span class="sxs-lookup"><span data-stu-id="787d0-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="787d0-778">Эти подключения шифруются по протоколу TLS с использованием предоставленного сертификата:</span><span class="sxs-lookup"><span data-stu-id="787d0-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="787d0-779">Также вы можете создать реализацию `IConnectionAdapter` для фильтрации подтверждений TLS для каждого соединения по конкретным шифрам:</span><span class="sxs-lookup"><span data-stu-id="787d0-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="787d0-780">*Выбор протокола из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="787d0-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> по умолчанию вызывает `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="787d0-782">В следующем примере *appsettings.json* для всех конечных точек Kestrel устанавливается протокол подключения по умолчанию (HTTP/1.1 и HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="787d0-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="787d0-783">В следующем примере файла конфигурации задается протокол соединения для конкретной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="787d0-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="787d0-784">Указанные в коде протоколы переопределяют значения, заданные в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="787d0-785">Конфигурация транспорта Libuv</span><span class="sxs-lookup"><span data-stu-id="787d0-785">Libuv transport configuration</span></span>

<span data-ttu-id="787d0-786">После выпуска ASP.NET Core 2.1 транспорт Kestrel по умолчанию основан не на Libuv, а на управляемых сокетах.</span><span class="sxs-lookup"><span data-stu-id="787d0-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="787d0-787">Это критическое изменение для приложений ASP.NET Core 2.0, которые обновляются до версии 2.1, если они вызывают <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> и зависят от одного из следующих пакетов:</span><span class="sxs-lookup"><span data-stu-id="787d0-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="787d0-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (прямая ссылка на пакет)</span><span class="sxs-lookup"><span data-stu-id="787d0-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="787d0-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="787d0-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="787d0-790">Для проектов, где требуется использовать Libuv:</span><span class="sxs-lookup"><span data-stu-id="787d0-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="787d0-791">Добавляют зависимость для пакета [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) к файлу проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="787d0-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="787d0-792">Вызов <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="787d0-793">Префиксы URL-адресов</span><span class="sxs-lookup"><span data-stu-id="787d0-793">URL prefixes</span></span>

<span data-ttu-id="787d0-794">Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.</span><span class="sxs-lookup"><span data-stu-id="787d0-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="787d0-795">Допустимы только префиксы URL-адресов HTTP.</span><span class="sxs-lookup"><span data-stu-id="787d0-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="787d0-796">Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="787d0-797">IPv4-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="787d0-798">`0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.</span><span class="sxs-lookup"><span data-stu-id="787d0-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="787d0-799">IPv6-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="787d0-800">`[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="787d0-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="787d0-801">Имя узла с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="787d0-802">Имена узлов, `*` и `+`, не являются особыми.</span><span class="sxs-lookup"><span data-stu-id="787d0-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="787d0-803">Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="787d0-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="787d0-804">Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер, такой как IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="787d0-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="787d0-805">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="787d0-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="787d0-806">Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="787d0-807">Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="787d0-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="787d0-808">Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается.</span><span class="sxs-lookup"><span data-stu-id="787d0-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="787d0-809">Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.</span><span class="sxs-lookup"><span data-stu-id="787d0-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="787d0-810">Фильтрация узлов</span><span class="sxs-lookup"><span data-stu-id="787d0-810">Host filtering</span></span>

<span data-ttu-id="787d0-811">Хотя Kestrel поддерживает конфигурации с использованием префиксов, такие как `http://example.com:5000`, Kestrel не учитывает имя узла.</span><span class="sxs-lookup"><span data-stu-id="787d0-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="787d0-812">Узел `localhost` является особым случаем, используемым для привязки к адресам замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="787d0-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="787d0-813">Любой узел, отличный от явного IP-адреса, привязывается ко всем общедоступным IP-адресам.</span><span class="sxs-lookup"><span data-stu-id="787d0-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="787d0-814">Заголовки `Host` не проверяются.</span><span class="sxs-lookup"><span data-stu-id="787d0-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="787d0-815">В качестве обходного решения используйте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="787d0-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="787d0-816">ПО промежуточного слоя фильтрации узла предоставляется пакетом [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), который входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или 2.2).</span><span class="sxs-lookup"><span data-stu-id="787d0-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="787d0-817">ПО промежуточного слоя добавляется в <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который вызывает <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="787d0-818">ПО промежуточного слоя фильтрации узлов отключено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="787d0-819">Чтобы включить ПО промежуточного слоя, определите ключ `AllowedHosts` в *appsettings.json* /*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="787d0-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="787d0-820">Значение представляет собой разделенный точками с запятой список имен узлов без номеров портов:</span><span class="sxs-lookup"><span data-stu-id="787d0-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="787d0-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="787d0-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="787d0-822">[ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer) имеет также параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="787d0-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="787d0-823">ПО промежуточного слоя перенаправления заголовков и ПО промежуточного слоя фильтрации узлов обладают сходными возможностями для различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="787d0-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="787d0-824">Параметр `AllowedHosts` с ПО промежуточного слоя перенаправления заголовков подходит для случаев, когда заголовок `Host` не сохраняется при переадресации запросов с помощью обратного прокси-сервера или подсистемы балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="787d0-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="787d0-825">Параметр `AllowedHosts` с ПО промежуточного слоя фильтрации узлов подходит для случаев, когда Kestrel используется в качестве общедоступного пограничного сервера или если заголовок `Host` пересылается напрямую.</span><span class="sxs-lookup"><span data-stu-id="787d0-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="787d0-826">Дополнительные сведения о ПО промежуточного слоя перенаправления заголовков см. в статье <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="787d0-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="787d0-827">Очистка запросов HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="787d0-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="787d0-828">Открытие HTTP-соединений занимает много времени.</span><span class="sxs-lookup"><span data-stu-id="787d0-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="787d0-829">Для протокола HTTPS это также требует больших ресурсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="787d0-830">Поэтому Kestrel пытается повторно использовать подключения по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="787d0-831">Чтобы разрешить повторное использование соединения, текст запроса должен быть полностью использован.</span><span class="sxs-lookup"><span data-stu-id="787d0-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="787d0-832">Приложение не всегда использует текст запроса, например запросы `POST`, в которых сервер возвращает ответ перенаправления или 404.</span><span class="sxs-lookup"><span data-stu-id="787d0-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="787d0-833">В случае перенаправления `POST`:</span><span class="sxs-lookup"><span data-stu-id="787d0-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="787d0-834">Возможно, клиент уже отправил часть данных `POST`.</span><span class="sxs-lookup"><span data-stu-id="787d0-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="787d0-835">Сервер записывает ответ 301.</span><span class="sxs-lookup"><span data-stu-id="787d0-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="787d0-836">Соединение нельзя использовать для нового запроса, пока не будут полностью прочитаны данные `POST` из предыдущего текста запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="787d0-837">Kestrel пытается очистить текст запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="787d0-838">Очистка текста запроса означает чтение и отмену данных без их обработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="787d0-839">Процесс очистки позволяет найти баланс между возможностью повторного использования соединения и временем, которое требуется для очистки оставшихся данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="787d0-840">Время ожидания очистки составляет 5 секунд. Этот параметр нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="787d0-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="787d0-841">Если все данные, указанные в заголовке `Content-Length` или `Transfer-Encoding`, не были считаны до истечения времени ожидания, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="787d0-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="787d0-842">Иногда может потребоваться немедленно завершить запрос до или после записи ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="787d0-843">Например, клиенты могут иметь ограничения на данные, поэтому ограничение передаваемых данных может иметь приоритет.</span><span class="sxs-lookup"><span data-stu-id="787d0-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="787d0-844">В таких случаях для завершения запроса вызовите [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) из контроллера, страницы Razor или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="787d0-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="787d0-845">Вызов `Abort` имеет определенные недостатки.</span><span class="sxs-lookup"><span data-stu-id="787d0-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="787d0-846">Создание новых подключений может выполняться очень медленно и требовать много ресурсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="787d0-847">Нет никакой гарантии, что клиент прочитал ответ перед закрытием соединения.</span><span class="sxs-lookup"><span data-stu-id="787d0-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="787d0-848">Вызов `Abort` следует использовать редко и только для серьезных, а не распространенных ошибок.</span><span class="sxs-lookup"><span data-stu-id="787d0-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="787d0-849">Вызывайте `Abort`, только когда нужно решить конкретную проблему.</span><span class="sxs-lookup"><span data-stu-id="787d0-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="787d0-850">Например, вызовите `Abort`, если вредоносные клиенты пытаются выполнить операцию `POST` с данными или если в клиентском коде есть ошибка, вызывающая большие или многочисленные запросы.</span><span class="sxs-lookup"><span data-stu-id="787d0-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="787d0-851">Не вызывайте `Abort` для распространенных ошибок, таких как HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="787d0-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="787d0-852">Вызов [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) перед вызовом `Abort` гарантирует, что сервер завершит запись ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="787d0-853">Однако поведение клиента не предсказуемо. Он может не считать ответ, прежде чем подключение будет прервано.</span><span class="sxs-lookup"><span data-stu-id="787d0-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="787d0-854">Этот процесс отличается для HTTP/2, так как протокол поддерживает прерывание отдельных потоков запросов без закрытия соединения.</span><span class="sxs-lookup"><span data-stu-id="787d0-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="787d0-855">5-секундное время ожидания очистки не применяется.</span><span class="sxs-lookup"><span data-stu-id="787d0-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="787d0-856">Если после завершения ответа сервер содержит непрочтенные данные текста запроса, он отправляет кадр HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="787d0-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="787d0-857">Дополнительные кадры данных текста запроса игнорируются.</span><span class="sxs-lookup"><span data-stu-id="787d0-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="787d0-858">По возможности клиентам лучше использовать заголовок запроса [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) и дожидаться ответа сервера перед началом отправки текста запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="787d0-859">Это дает клиенту возможность проверить ответ и прервать операцию перед отправкой ненужных данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="787d0-860">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="787d0-860">Additional resources</span></span>

* <span data-ttu-id="787d0-861">При использовании сокетов UNIX в Linux сокет не удаляется автоматически по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="787d0-862">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="787d0-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="787d0-863">RFC 7230: синтаксис и маршрутизация сообщений (раздел 5.4: узел)</span><span class="sxs-lookup"><span data-stu-id="787d0-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="787d0-864">Kestrel — это кроссплатформенный [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="787d0-865">Веб-сервер Kestrel по умолчанию включается в шаблоны проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="787d0-866">Kestrel поддерживается в следующих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="787d0-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="787d0-867">HTTPS</span></span>
* <span data-ttu-id="787d0-868">Непрозрачное обновление для поддержки [WebSocket](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="787d0-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="787d0-869">Сокеты UNIX для повышения производительности при работе за Nginx</span><span class="sxs-lookup"><span data-stu-id="787d0-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="787d0-870">Kestrel поддерживается на всех платформах и во всех версиях, поддерживаемых .NET Core.</span><span class="sxs-lookup"><span data-stu-id="787d0-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="787d0-871">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="787d0-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="787d0-872">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="787d0-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="787d0-873">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="787d0-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="787d0-874">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="787d0-875">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="787d0-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="787d0-877">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="787d0-879">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="787d0-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="787d0-880">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="787d0-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="787d0-881">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="787d0-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="787d0-882">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="787d0-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="787d0-883">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="787d0-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="787d0-884">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="787d0-884">A reverse proxy:</span></span>

* <span data-ttu-id="787d0-885">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="787d0-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="787d0-886">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="787d0-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="787d0-887">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="787d0-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="787d0-888">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="787d0-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="787d0-889">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="787d0-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="787d0-890">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="787d0-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="787d0-891">Условия использования Kestrel в приложениях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="787d0-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="787d0-892">Пакет [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) входит в состав [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="787d0-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="787d0-893">Шаблоны проектов ASP.NET Core используют Kestrel по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="787d0-894">В файле *Program.cs* код шаблона вызывает метод <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который в фоновом режиме вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>.</span><span class="sxs-lookup"><span data-stu-id="787d0-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="787d0-895">Чтобы задать дополнительную конфигурацию после вызова `CreateDefaultBuilder`, вызовите <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="787d0-896">Дополнительные сведения о `CreateDefaultBuilder` и построении узла, см. в разделе *Настройка узла*, разделы <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="787d0-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="787d0-897">Параметры Kestrel</span><span class="sxs-lookup"><span data-stu-id="787d0-897">Kestrel options</span></span>

<span data-ttu-id="787d0-898">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="787d0-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="787d0-899">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="787d0-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="787d0-900">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="787d0-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="787d0-901">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="787d0-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="787d0-902">Параметры Kestrel, которые настраиваются в C# коде в следующих примерах, можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="787d0-903">Например, поставщик конфигурации файла может загрузить конфигурацию Kestrel из файла *appsettings.json* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="787d0-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="787d0-904">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="787d0-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="787d0-905">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="787d0-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="787d0-906">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="787d0-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="787d0-907">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="787d0-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="787d0-908">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="787d0-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="787d0-909">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="787d0-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="787d0-910">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="787d0-911">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="787d0-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="787d0-912">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="787d0-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="787d0-913">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="787d0-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="787d0-914">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="787d0-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="787d0-915">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="787d0-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="787d0-916">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="787d0-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="787d0-917">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="787d0-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="787d0-918">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="787d0-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="787d0-919">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="787d0-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="787d0-920">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="787d0-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="787d0-921">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="787d0-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="787d0-922">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="787d0-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="787d0-923">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="787d0-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="787d0-924">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="787d0-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="787d0-925">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="787d0-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="787d0-926">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="787d0-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="787d0-927">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено, так как оно уже задается IIS.</span><span class="sxs-lookup"><span data-stu-id="787d0-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="787d0-928">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="787d0-929">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="787d0-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="787d0-930">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня; в течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="787d0-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="787d0-931">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="787d0-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="787d0-932">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="787d0-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="787d0-933">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="787d0-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="787d0-934">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="787d0-935">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="787d0-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="787d0-936">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="787d0-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="787d0-937">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="787d0-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="787d0-938">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="787d0-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="787d0-939">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="787d0-939">Synchronous I/O</span></span>

<span data-ttu-id="787d0-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="787d0-941">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="787d0-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="787d0-942">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="787d0-943">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="787d0-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="787d0-944">В примере ниже отключаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="787d0-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="787d0-945">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="787d0-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="787d0-946">Конфигурация конечной точки</span><span class="sxs-lookup"><span data-stu-id="787d0-946">Endpoint configuration</span></span>

<span data-ttu-id="787d0-947">По умолчанию платформа ASP.NET Core привязана к:</span><span class="sxs-lookup"><span data-stu-id="787d0-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="787d0-948">`https://localhost:5001` (если присутствует локальный сертификат разработки)</span><span class="sxs-lookup"><span data-stu-id="787d0-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="787d0-949">Укажите URL-адреса с помощью следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="787d0-949">Specify URLs using the:</span></span>

* <span data-ttu-id="787d0-950">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="787d0-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="787d0-951">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="787d0-952">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="787d0-953">Метод расширения `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="787d0-954">Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="787d0-955">Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="787d0-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="787d0-956">Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="787d0-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="787d0-957">Сертификат разработки создается, когда:</span><span class="sxs-lookup"><span data-stu-id="787d0-957">A development certificate is created:</span></span>

* <span data-ttu-id="787d0-958">установлен [пакет SDK для .NET Core](/dotnet/core/sdk);</span><span class="sxs-lookup"><span data-stu-id="787d0-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="787d0-959">используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="787d0-960">В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="787d0-961">Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="787d0-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="787d0-962">Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="787d0-963">`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="787d0-964">Конфигурация `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="787d0-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="787d0-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="787d0-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="787d0-966">Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="787d0-967">Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="787d0-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="787d0-968">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="787d0-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="787d0-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="787d0-970">Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="787d0-971">Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.</span><span class="sxs-lookup"><span data-stu-id="787d0-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="787d0-972">К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="787d0-973">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="787d0-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="787d0-974">Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="787d0-975">Для Kestrel конфигурация должна быть ограничена разделом конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="787d0-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="787d0-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="787d0-977">Настройте Kestrel для использования протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="787d0-978">Расширения `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="787d0-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="787d0-979">`UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="787d0-980">Создает исключение, если сертификат по умолчанию не настроен.</span><span class="sxs-lookup"><span data-stu-id="787d0-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="787d0-981">Параметры `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="787d0-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="787d0-982">`filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="787d0-983">`password` — это пароль для доступа к данным сертификата X.509.</span><span class="sxs-lookup"><span data-stu-id="787d0-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="787d0-984">`configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="787d0-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="787d0-985">Возвращает `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="787d0-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="787d0-986">`storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="787d0-987">`subject` — это имя субъекта для сертификата.</span><span class="sxs-lookup"><span data-stu-id="787d0-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="787d0-988">`allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.</span><span class="sxs-lookup"><span data-stu-id="787d0-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="787d0-989">`location` — это расположение хранилища, из которого загружается сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="787d0-990">`serverCertificate` — это сертификат X.509.</span><span class="sxs-lookup"><span data-stu-id="787d0-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="787d0-991">В рабочей среде необходимо явно настроить HTTPS.</span><span class="sxs-lookup"><span data-stu-id="787d0-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="787d0-992">Как минимум необходимо указать сертификат по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="787d0-993">Поддерживаемые конфигурации, описанные далее:</span><span class="sxs-lookup"><span data-stu-id="787d0-993">Supported configurations described next:</span></span>

* <span data-ttu-id="787d0-994">Отсутствие конфигурации</span><span class="sxs-lookup"><span data-stu-id="787d0-994">No configuration</span></span>
* <span data-ttu-id="787d0-995">Замена сертификата по умолчанию из конфигурации</span><span class="sxs-lookup"><span data-stu-id="787d0-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="787d0-996">Изменение значений по умолчанию в коде</span><span class="sxs-lookup"><span data-stu-id="787d0-996">Change the defaults in code</span></span>

<span data-ttu-id="787d0-997">*Отсутствие конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-997">*No configuration*</span></span>

<span data-ttu-id="787d0-998">Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="787d0-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="787d0-999">*Замена сертификата по умолчанию из конфигурации*</span><span class="sxs-lookup"><span data-stu-id="787d0-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="787d0-1000">`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="787d0-1001">Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="787d0-1002">Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="787d0-1003">В следующем примере *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="787d0-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="787d0-1004">Установите для **AllowInvalid** значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).</span><span class="sxs-lookup"><span data-stu-id="787d0-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="787d0-1005">Любая конечная точка HTTPS, которая не указывает сертификат (**HttpsDefaultCert** в следующем примере), будет использовать сертификат, определенный в разделе **Certificates** (Сертификаты) > **Default** (По умолчанию), или сертификат разработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="787d0-1006">Вместо использования параметров **Path** и **Password** для узла сертификата можно указать сертификат с помощью полей хранилища сертификатов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="787d0-1007">Например, сертификат из раздела **Сертификаты** > **По умолчанию** можно указать следующим образом:</span><span class="sxs-lookup"><span data-stu-id="787d0-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="787d0-1008">Примечания к схеме.</span><span class="sxs-lookup"><span data-stu-id="787d0-1008">Schema notes:</span></span>

* <span data-ttu-id="787d0-1009">Регистр букв в именах конечных точек не учитывается.</span><span class="sxs-lookup"><span data-stu-id="787d0-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="787d0-1010">Например, `HTTPS` и `Https` являются допустимыми.</span><span class="sxs-lookup"><span data-stu-id="787d0-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="787d0-1011">Параметр `Url` является обязательным для каждой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="787d0-1012">Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.</span><span class="sxs-lookup"><span data-stu-id="787d0-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="787d0-1013">Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их.</span><span class="sxs-lookup"><span data-stu-id="787d0-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="787d0-1014">Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="787d0-1015">Раздел `Certificate` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="787d0-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="787d0-1016">Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в предыдущих сценариях.</span><span class="sxs-lookup"><span data-stu-id="787d0-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="787d0-1017">Если значений по умолчанию нет, сервер выдает исключение и не запускается.</span><span class="sxs-lookup"><span data-stu-id="787d0-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="787d0-1018">Раздел `Certificate` поддерживает сертификаты **Path**&ndash;**Password** и **Subject**&ndash;**Store**.</span><span class="sxs-lookup"><span data-stu-id="787d0-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="787d0-1019">Таким образом, можно определить любое количество конечных точек, если это не приводит к конфликту портов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="787d0-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает `KestrelConfigurationLoader` с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:</span><span class="sxs-lookup"><span data-stu-id="787d0-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="787d0-1021">Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="787d0-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="787d0-1022">Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.</span><span class="sxs-lookup"><span data-stu-id="787d0-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="787d0-1023">Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом.</span><span class="sxs-lookup"><span data-stu-id="787d0-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="787d0-1024">Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах.</span><span class="sxs-lookup"><span data-stu-id="787d0-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="787d0-1025">Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="787d0-1026">`KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте.</span><span class="sxs-lookup"><span data-stu-id="787d0-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="787d0-1027">Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="787d0-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="787d0-1028">*Изменение значений по умолчанию в коде*</span><span class="sxs-lookup"><span data-stu-id="787d0-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="787d0-1029">Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии.</span><span class="sxs-lookup"><span data-stu-id="787d0-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="787d0-1030">Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.</span><span class="sxs-lookup"><span data-stu-id="787d0-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="787d0-1031">*Поддержка Kestrel для SNI*</span><span class="sxs-lookup"><span data-stu-id="787d0-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="787d0-1032">Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте.</span><span class="sxs-lookup"><span data-stu-id="787d0-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="787d0-1033">Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="787d0-1034">Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.</span><span class="sxs-lookup"><span data-stu-id="787d0-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="787d0-1035">Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="787d0-1036">Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат.</span><span class="sxs-lookup"><span data-stu-id="787d0-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="787d0-1037">Поддержка SNI требует:</span><span class="sxs-lookup"><span data-stu-id="787d0-1037">SNI support requires:</span></span>

* <span data-ttu-id="787d0-1038">Запуск на целевой платформе `netcoreapp2.1` или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="787d0-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="787d0-1039">В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="787d0-1040">`name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.</span><span class="sxs-lookup"><span data-stu-id="787d0-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="787d0-1041">Все веб-сайты выполняются на одном и том же экземпляре Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="787d0-1042">Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="787d0-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="787d0-1043">Ведение журнала подключения</span><span class="sxs-lookup"><span data-stu-id="787d0-1043">Connection logging</span></span>

<span data-ttu-id="787d0-1044">Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения.</span><span class="sxs-lookup"><span data-stu-id="787d0-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="787d0-1045">Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами.</span><span class="sxs-lookup"><span data-stu-id="787d0-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="787d0-1046">Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="787d0-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="787d0-1047">Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик.</span><span class="sxs-lookup"><span data-stu-id="787d0-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="787d0-1048">Привязка к TCP-сокету</span><span class="sxs-lookup"><span data-stu-id="787d0-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="787d0-1049">Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:</span><span class="sxs-lookup"><span data-stu-id="787d0-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="787d0-1050">В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="787d0-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="787d0-1051">С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.</span><span class="sxs-lookup"><span data-stu-id="787d0-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="787d0-1052">Привязка к сокету UNIX</span><span class="sxs-lookup"><span data-stu-id="787d0-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="787d0-1053">Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>, чтобы улучшить производительность Nginx, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="787d0-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="787d0-1054">В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="787d0-1055">`{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (как `kestrel-test.sock` в предыдущем примере).</span><span class="sxs-lookup"><span data-stu-id="787d0-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="787d0-1056">Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="787d0-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="787d0-1057">Порт 0</span><span class="sxs-lookup"><span data-stu-id="787d0-1057">Port 0</span></span>

<span data-ttu-id="787d0-1058">Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту.</span><span class="sxs-lookup"><span data-stu-id="787d0-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="787d0-1059">Следующий пример показывает, как определить, к какому порту фактически привязан Kestrel во время выполнения:</span><span class="sxs-lookup"><span data-stu-id="787d0-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="787d0-1060">Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:</span><span class="sxs-lookup"><span data-stu-id="787d0-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="787d0-1061">Ограничения</span><span class="sxs-lookup"><span data-stu-id="787d0-1061">Limitations</span></span>

<span data-ttu-id="787d0-1062">Настройте конечные точки с помощью следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="787d0-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="787d0-1063">Аргументы командной строки `--urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="787d0-1064">Ключ конфигурации узла `urls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="787d0-1065">Переменная среды `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="787d0-1066">Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel.</span><span class="sxs-lookup"><span data-stu-id="787d0-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="787d0-1067">Не забывайте о следующих ограничениях.</span><span class="sxs-lookup"><span data-stu-id="787d0-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="787d0-1068">С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этом разделе).</span><span class="sxs-lookup"><span data-stu-id="787d0-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="787d0-1069">Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="787d0-1070">Конфигурация конечной точки IIS</span><span class="sxs-lookup"><span data-stu-id="787d0-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="787d0-1071">При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="787d0-1072">Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="787d0-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="787d0-1073">Конфигурация транспорта Libuv</span><span class="sxs-lookup"><span data-stu-id="787d0-1073">Libuv transport configuration</span></span>

<span data-ttu-id="787d0-1074">После выпуска ASP.NET Core 2.1 транспорт Kestrel по умолчанию основан не на Libuv, а на управляемых сокетах.</span><span class="sxs-lookup"><span data-stu-id="787d0-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="787d0-1075">Это критическое изменение для приложений ASP.NET Core 2.0, которые обновляются до версии 2.1, если они вызывают <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> и зависят от одного из следующих пакетов:</span><span class="sxs-lookup"><span data-stu-id="787d0-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="787d0-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (прямая ссылка на пакет)</span><span class="sxs-lookup"><span data-stu-id="787d0-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="787d0-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="787d0-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="787d0-1078">Для проектов, где требуется использовать Libuv:</span><span class="sxs-lookup"><span data-stu-id="787d0-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="787d0-1079">Добавляют зависимость для пакета [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) к файлу проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="787d0-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="787d0-1080">Вызов <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="787d0-1081">Префиксы URL-адресов</span><span class="sxs-lookup"><span data-stu-id="787d0-1081">URL prefixes</span></span>

<span data-ttu-id="787d0-1082">Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="787d0-1083">Допустимы только префиксы URL-адресов HTTP.</span><span class="sxs-lookup"><span data-stu-id="787d0-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="787d0-1084">Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="787d0-1085">IPv4-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="787d0-1086">`0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.</span><span class="sxs-lookup"><span data-stu-id="787d0-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="787d0-1087">IPv6-адрес с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="787d0-1088">`[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="787d0-1089">Имя узла с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="787d0-1090">Имена узлов, `*` и `+`, не являются особыми.</span><span class="sxs-lookup"><span data-stu-id="787d0-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="787d0-1091">Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="787d0-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="787d0-1092">Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер, такой как IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="787d0-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="787d0-1093">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="787d0-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="787d0-1094">Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта</span><span class="sxs-lookup"><span data-stu-id="787d0-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="787d0-1095">Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6.</span><span class="sxs-lookup"><span data-stu-id="787d0-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="787d0-1096">Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается.</span><span class="sxs-lookup"><span data-stu-id="787d0-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="787d0-1097">Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.</span><span class="sxs-lookup"><span data-stu-id="787d0-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="787d0-1098">Фильтрация узлов</span><span class="sxs-lookup"><span data-stu-id="787d0-1098">Host filtering</span></span>

<span data-ttu-id="787d0-1099">Хотя Kestrel поддерживает конфигурации с использованием префиксов, такие как `http://example.com:5000`, Kestrel не учитывает имя узла.</span><span class="sxs-lookup"><span data-stu-id="787d0-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="787d0-1100">Узел `localhost` является особым случаем, используемым для привязки к адресам замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="787d0-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="787d0-1101">Любой узел, отличный от явного IP-адреса, привязывается ко всем общедоступным IP-адресам.</span><span class="sxs-lookup"><span data-stu-id="787d0-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="787d0-1102">Заголовки `Host` не проверяются.</span><span class="sxs-lookup"><span data-stu-id="787d0-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="787d0-1103">В качестве обходного решения используйте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="787d0-1104">ПО промежуточного слоя фильтрации узла предоставляется пакетом [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering), который входит в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 или 2.2).</span><span class="sxs-lookup"><span data-stu-id="787d0-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="787d0-1105">ПО промежуточного слоя добавляется в <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, который вызывает <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="787d0-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="787d0-1106">ПО промежуточного слоя фильтрации узлов отключено по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="787d0-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="787d0-1107">Чтобы включить ПО промежуточного слоя, определите ключ `AllowedHosts` в *appsettings.json* /*appsettings.\<EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="787d0-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="787d0-1108">Значение представляет собой разделенный точками с запятой список имен узлов без номеров портов:</span><span class="sxs-lookup"><span data-stu-id="787d0-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="787d0-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="787d0-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="787d0-1110">[ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer) имеет также параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="787d0-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="787d0-1111">ПО промежуточного слоя перенаправления заголовков и ПО промежуточного слоя фильтрации узлов обладают сходными возможностями для различных сценариев.</span><span class="sxs-lookup"><span data-stu-id="787d0-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="787d0-1112">Параметр `AllowedHosts` с ПО промежуточного слоя перенаправления заголовков подходит для случаев, когда заголовок `Host` не сохраняется при переадресации запросов с помощью обратного прокси-сервера или подсистемы балансировки нагрузки.</span><span class="sxs-lookup"><span data-stu-id="787d0-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="787d0-1113">Параметр `AllowedHosts` с ПО промежуточного слоя фильтрации узлов подходит для случаев, когда Kestrel используется в качестве общедоступного пограничного сервера или если заголовок `Host` пересылается напрямую.</span><span class="sxs-lookup"><span data-stu-id="787d0-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="787d0-1114">Дополнительные сведения о ПО промежуточного слоя перенаправления заголовков см. в статье <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="787d0-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="787d0-1115">Очистка запросов HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="787d0-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="787d0-1116">Открытие HTTP-соединений занимает много времени.</span><span class="sxs-lookup"><span data-stu-id="787d0-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="787d0-1117">Для протокола HTTPS это также требует больших ресурсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="787d0-1118">Поэтому Kestrel пытается повторно использовать подключения по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="787d0-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="787d0-1119">Чтобы разрешить повторное использование соединения, текст запроса должен быть полностью использован.</span><span class="sxs-lookup"><span data-stu-id="787d0-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="787d0-1120">Приложение не всегда использует текст запроса, например запросы `POST`, в которых сервер возвращает ответ перенаправления или 404.</span><span class="sxs-lookup"><span data-stu-id="787d0-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="787d0-1121">В случае перенаправления `POST`:</span><span class="sxs-lookup"><span data-stu-id="787d0-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="787d0-1122">Возможно, клиент уже отправил часть данных `POST`.</span><span class="sxs-lookup"><span data-stu-id="787d0-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="787d0-1123">Сервер записывает ответ 301.</span><span class="sxs-lookup"><span data-stu-id="787d0-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="787d0-1124">Соединение нельзя использовать для нового запроса, пока не будут полностью прочитаны данные `POST` из предыдущего текста запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="787d0-1125">Kestrel пытается очистить текст запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="787d0-1126">Очистка текста запроса означает чтение и отмену данных без их обработки.</span><span class="sxs-lookup"><span data-stu-id="787d0-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="787d0-1127">Процесс очистки позволяет найти баланс между возможностью повторного использования соединения и временем, которое требуется для очистки оставшихся данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="787d0-1128">Время ожидания очистки составляет 5 секунд. Этот параметр нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="787d0-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="787d0-1129">Если все данные, указанные в заголовке `Content-Length` или `Transfer-Encoding`, не были считаны до истечения времени ожидания, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="787d0-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="787d0-1130">Иногда может потребоваться немедленно завершить запрос до или после записи ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="787d0-1131">Например, клиенты могут иметь ограничения на данные, поэтому ограничение передаваемых данных может иметь приоритет.</span><span class="sxs-lookup"><span data-stu-id="787d0-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="787d0-1132">В таких случаях для завершения запроса вызовите [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) из контроллера, страницы Razor или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="787d0-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="787d0-1133">Вызов `Abort` имеет определенные недостатки.</span><span class="sxs-lookup"><span data-stu-id="787d0-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="787d0-1134">Создание новых подключений может выполняться очень медленно и требовать много ресурсов.</span><span class="sxs-lookup"><span data-stu-id="787d0-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="787d0-1135">Нет никакой гарантии, что клиент прочитал ответ перед закрытием соединения.</span><span class="sxs-lookup"><span data-stu-id="787d0-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="787d0-1136">Вызов `Abort` следует использовать редко и только для серьезных, а не распространенных ошибок.</span><span class="sxs-lookup"><span data-stu-id="787d0-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="787d0-1137">Вызывайте `Abort`, только когда нужно решить конкретную проблему.</span><span class="sxs-lookup"><span data-stu-id="787d0-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="787d0-1138">Например, вызовите `Abort`, если вредоносные клиенты пытаются выполнить операцию `POST` с данными или если в клиентском коде есть ошибка, вызывающая большие или многочисленные запросы.</span><span class="sxs-lookup"><span data-stu-id="787d0-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="787d0-1139">Не вызывайте `Abort` для распространенных ошибок, таких как HTTP 404 (не найдено).</span><span class="sxs-lookup"><span data-stu-id="787d0-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="787d0-1140">Вызов [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) перед вызовом `Abort` гарантирует, что сервер завершит запись ответа.</span><span class="sxs-lookup"><span data-stu-id="787d0-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="787d0-1141">Однако поведение клиента не предсказуемо. Он может не считать ответ, прежде чем подключение будет прервано.</span><span class="sxs-lookup"><span data-stu-id="787d0-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="787d0-1142">Этот процесс отличается для HTTP/2, так как протокол поддерживает прерывание отдельных потоков запросов без закрытия соединения.</span><span class="sxs-lookup"><span data-stu-id="787d0-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="787d0-1143">5-секундное время ожидания очистки не применяется.</span><span class="sxs-lookup"><span data-stu-id="787d0-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="787d0-1144">Если после завершения ответа сервер содержит непрочтенные данные текста запроса, он отправляет кадр HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="787d0-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="787d0-1145">Дополнительные кадры данных текста запроса игнорируются.</span><span class="sxs-lookup"><span data-stu-id="787d0-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="787d0-1146">По возможности клиентам лучше использовать заголовок запроса [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) и дожидаться ответа сервера перед началом отправки текста запроса.</span><span class="sxs-lookup"><span data-stu-id="787d0-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="787d0-1147">Это дает клиенту возможность проверить ответ и прервать операцию перед отправкой ненужных данных.</span><span class="sxs-lookup"><span data-stu-id="787d0-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="787d0-1148">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="787d0-1148">Additional resources</span></span>

* <span data-ttu-id="787d0-1149">При использовании сокетов UNIX в Linux сокет не удаляется автоматически по завершении работы приложения.</span><span class="sxs-lookup"><span data-stu-id="787d0-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="787d0-1150">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="787d0-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="787d0-1151">RFC 7230: синтаксис и маршрутизация сообщений (раздел 5.4: узел)</span><span class="sxs-lookup"><span data-stu-id="787d0-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
