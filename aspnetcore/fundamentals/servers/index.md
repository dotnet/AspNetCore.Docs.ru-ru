---
title: Реализации веб-сервера в ASP.NET Core
author: rick-anderson
description: Откройте возможности веб-серверов Kestrel и HTTP.sys для ASP.NET Core. Рекомендации по выбору сервера и сведения о сценариях использования обратного прокси-сервера.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 49e299ed00ea0e5d54c1ba795971da379cd5b695
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253141"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="0559b-104">Реализации веб-сервера в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0559b-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="0559b-105">Авторы: [Том Дисктра](https://github.com/tdykstra) (Tom Dykstra), [Стив Смит](https://ardalis.com/) (Steve Smith), [Стивен Хальтер](https://twitter.com/halter73) (Stephen Halter) и [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="0559b-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="0559b-106">Приложение ASP.NET Core выполняется вместе с внутрипроцессной реализацией HTTP-сервера.</span><span class="sxs-lookup"><span data-stu-id="0559b-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="0559b-107">Реализация сервера прослушивает HTTP-запросы и передает их в приложение как набор [функций запросов](xref:fundamentals/request-features), объединенных в <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="0559b-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="0559b-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0559b-108">Kestrel</span></span>

<span data-ttu-id="0559b-109">Kestrel — это веб-сервер, который по умолчанию указывается шаблонами проектов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0559b-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="0559b-110">Kestrel используется в следующих сценариях:</span><span class="sxs-lookup"><span data-stu-id="0559b-110">Use Kestrel:</span></span>

* <span data-ttu-id="0559b-111">Сам по себе как пограничный сервер обработки запросов непосредственно из сети, включая Интернет.</span><span class="sxs-lookup"><span data-stu-id="0559b-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="0559b-113">С *обратным прокси-сервером*, таким как [службы IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="0559b-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="0559b-114">Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0559b-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="0559b-116">Поддерживается любая из этих конфигураций размещения &mdash; с обратным прокси-сервером и без него.</span><span class="sxs-lookup"><span data-stu-id="0559b-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="0559b-117">Инструкции по настройке Kestrel и сведения о том, когда следует использовать Kestrel в конфигурации обратного прокси-сервера, см. в статье <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="0559b-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="0559b-118">Windows</span><span class="sxs-lookup"><span data-stu-id="0559b-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="0559b-119">В состав ASP.NET Core входит следующее:</span><span class="sxs-lookup"><span data-stu-id="0559b-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="0559b-120">Сервер [Kestrel](xref:fundamentals/servers/kestrel) — это реализация кроссплатформенного HTTP-сервера по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0559b-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="0559b-121">HTTP-сервер IIS — это [внутрипроцессный сервер для службы IIS](#hosting-models).</span><span class="sxs-lookup"><span data-stu-id="0559b-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="0559b-122">[Сервер HTTP.sys](xref:fundamentals/servers/httpsys) — это HTTP-сервер, предназначенный только для Windows и основанный на [драйвере ядра HTTP.sys и API HTTP-сервера](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="0559b-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="0559b-123">При использовании [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) или [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) приложение запускается одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="0559b-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="0559b-124">В том же процессе, что и рабочий процесс IIS ([модель внутрипроцессного размещения](#hosting-models)), с использованием HTTP-сервера IIS.</span><span class="sxs-lookup"><span data-stu-id="0559b-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="0559b-125">*Внутрипроцессное размещение* является рекомендуемой конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="0559b-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="0559b-126">В процессе отдельно от рабочего процесса IIS ([модель внепроцессного размещения](#hosting-models)) с использованием [сервера Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="0559b-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="0559b-127">[Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) — это собственный модуль IIS, который обрабатывает собственные запросы IIS между службами IIS и HTTP-сервером IIS (внутрипроцессно) или сервером Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0559b-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="0559b-128">Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="0559b-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="0559b-129">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="0559b-129">Hosting models</span></span>

<span data-ttu-id="0559b-130">При внутрипроцессном размещении приложение ASP.NET Core выполняется в том же процессе, что и рабочий процесс IIS.</span><span class="sxs-lookup"><span data-stu-id="0559b-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="0559b-131">При этом повышается производительность по сравнению с внепроцессным размещением, так как запросы не передаются через адаптер замыкания на себя (сетевой интерфейс, который возвращает исходящий сетевой трафик на тот же компьютер).</span><span class="sxs-lookup"><span data-stu-id="0559b-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="0559b-132">IIS обрабатывает управление процессом с помощью [службы активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0559b-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0559b-133">При внепроцессном размещении приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, а модуль управляет процессами.</span><span class="sxs-lookup"><span data-stu-id="0559b-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="0559b-134">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="0559b-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="0559b-135">Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0559b-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0559b-136">Дополнительные сведения и инструкции по настройке см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="0559b-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="0559b-137">macOS</span><span class="sxs-lookup"><span data-stu-id="0559b-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="0559b-138">ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0559b-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="0559b-139">Linux</span><span class="sxs-lookup"><span data-stu-id="0559b-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="0559b-140">ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0559b-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="0559b-141">Windows</span><span class="sxs-lookup"><span data-stu-id="0559b-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="0559b-142">В состав ASP.NET Core входит следующее:</span><span class="sxs-lookup"><span data-stu-id="0559b-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="0559b-143">Сервер [Kestrel](xref:fundamentals/servers/kestrel) — кроссплатформенный HTTP-сервер по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0559b-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="0559b-144">[Сервер HTTP.sys](xref:fundamentals/servers/httpsys) — это HTTP-сервер, предназначенный только для Windows и основанный на [драйвере ядра HTTP.sys и API HTTP-сервера](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="0559b-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="0559b-145">При использовании [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) или [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) приложение запускается отдельно от рабочего процесса IIS (*внепроцессно*) с [сервером Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="0559b-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="0559b-146">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="0559b-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="0559b-147">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="0559b-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="0559b-148">Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0559b-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0559b-149">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.</span><span class="sxs-lookup"><span data-stu-id="0559b-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Модуль ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="0559b-151">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="0559b-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="0559b-152">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="0559b-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="0559b-153">Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.</span><span class="sxs-lookup"><span data-stu-id="0559b-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="0559b-154">Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="0559b-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="0559b-155">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="0559b-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="0559b-156">Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0559b-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="0559b-157">После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0559b-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="0559b-158">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="0559b-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="0559b-159">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0559b-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="0559b-160">Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.</span><span class="sxs-lookup"><span data-stu-id="0559b-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="0559b-161">Инструкции по настройке модуля ASP.NET Core и IIS см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="0559b-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="0559b-162">macOS</span><span class="sxs-lookup"><span data-stu-id="0559b-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="0559b-163">ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0559b-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="0559b-164">Linux</span><span class="sxs-lookup"><span data-stu-id="0559b-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="0559b-165">ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="0559b-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="0559b-166">Nginx с Kestrel</span><span class="sxs-lookup"><span data-stu-id="0559b-166">Nginx with Kestrel</span></span>

<span data-ttu-id="0559b-167">Сведения о том, как использовать Nginx в Linux в качестве обратного прокси-сервера для Kestrel, см. в <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="0559b-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="0559b-168">Apache с Kestrel</span><span class="sxs-lookup"><span data-stu-id="0559b-168">Apache with Kestrel</span></span>

<span data-ttu-id="0559b-169">Сведения о том, как использовать Apache в Linux в качестве обратного прокси-сервера для Kestrel, см. в <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="0559b-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="0559b-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0559b-170">HTTP.sys</span></span>

<span data-ttu-id="0559b-171">Если приложение ASP.NET Core запускается в Windows, вместо Kestrel можно использовать HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0559b-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="0559b-172">Как правило, для оптимальной производительности рекомендуется Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0559b-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="0559b-173">HTTP.sys можно использовать в сценариях, где приложение имеет доступ к Интернету и необходимые возможности поддерживаются HTTP.sys, но не Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0559b-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="0559b-174">Для получения дополнительной информации см. <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="0559b-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys взаимодействует с Интернетом напрямую](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="0559b-176">HTTP.sys можно также использовать для приложений, которые имеют доступ только к внутренней сети.</span><span class="sxs-lookup"><span data-stu-id="0559b-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys взаимодействует с внутренней сетью напрямую](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="0559b-178">Инструкции по настройке HTTP.sys см. в статье <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="0559b-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="0559b-179">Инфраструктура сервера ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0559b-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="0559b-180"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>, доступный в методе `Startup.Configure`, предоставляет свойство <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> типа <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="0559b-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="0559b-181">Kestrel и HTTP.sys предоставляют только один компонент, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, но разные реализации сервера могут предоставлять дополнительные возможности.</span><span class="sxs-lookup"><span data-stu-id="0559b-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="0559b-182">`IServerAddressesFeature` можно использовать для того, чтобы узнать, какой порт в реализации сервера привязан к среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="0559b-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="0559b-183">Пользовательские серверы</span><span class="sxs-lookup"><span data-stu-id="0559b-183">Custom servers</span></span>

<span data-ttu-id="0559b-184">Если встроенные серверы не отвечают требованиям приложения, можно создать реализацию пользовательского сервера.</span><span class="sxs-lookup"><span data-stu-id="0559b-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="0559b-185">В [руководстве по открытому веб-интерфейсу .NET (OWIN)](xref:fundamentals/owin) демонстрируется запись реализации <xref:Microsoft.AspNetCore.Hosting.Server.IServer> на основе [Nowin](https://github.com/Bobris/Nowin).</span><span class="sxs-lookup"><span data-stu-id="0559b-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="0559b-186">Требуют реализации только интерфейсы компонентов, используемых приложением, но как минимум должны поддерживаться <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> и <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>.</span><span class="sxs-lookup"><span data-stu-id="0559b-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="0559b-187">Запуск сервера</span><span class="sxs-lookup"><span data-stu-id="0559b-187">Server startup</span></span>

<span data-ttu-id="0559b-188">Сервер запускается, когда интегрированная среда разработки (IDE) или редактор запускает приложение:</span><span class="sxs-lookup"><span data-stu-id="0559b-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="0559b-189">[Visual Studio](https://visualstudio.microsoft.com). Вы можете использовать профили запуска для запуска приложения и сервера с помощью [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) или консоли.</span><span class="sxs-lookup"><span data-stu-id="0559b-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="0559b-190">[Visual Studio Code](https://code.visualstudio.com/). Приложение и сервер запускаются решением [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), которое активирует отладчик CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="0559b-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="0559b-191">[Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/). Приложение и сервер запускаются отладчиком "мягкого режима" [Mono Soft Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="0559b-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="0559b-192">При запуске приложения из командной строки в папке проекта [dotnet run](/dotnet/core/tools/dotnet-run) запускает приложение и сервер (только Kestrel и HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="0559b-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="0559b-193">Конфигурация определяется параметром `-c|--configuration`, который может принимать значение `Debug` (по умолчанию) или `Release`.</span><span class="sxs-lookup"><span data-stu-id="0559b-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="0559b-194">Файл *launchSettings.json* предоставляет конфигурацию при запуске приложения с помощью `dotnet run` или с помощью отладчика, встроенного в инструментарий, например Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0559b-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="0559b-195">Если профили запуска указаны в файле *launchSettings.json*, используйте параметр `--launch-profile {PROFILE NAME}` с командой `dotnet run` или выберите профиль в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0559b-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="0559b-196">Дополнительные сведения см. в статьях [dotnet run](/dotnet/core/tools/dotnet-run) и [Упаковка дистрибутивов .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="0559b-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="0559b-197">Поддержка HTTP/2</span><span class="sxs-lookup"><span data-stu-id="0559b-197">HTTP/2 support</span></span>

<span data-ttu-id="0559b-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается в ASP.NET Core для следующих сценариев развертывания:</span><span class="sxs-lookup"><span data-stu-id="0559b-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="0559b-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0559b-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="0559b-200">Операционная система</span><span class="sxs-lookup"><span data-stu-id="0559b-200">Operating system</span></span>
    * <span data-ttu-id="0559b-201">Windows Server 2016 / Windows 10 или более поздних версий&dagger;</span><span class="sxs-lookup"><span data-stu-id="0559b-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="0559b-202">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="0559b-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="0559b-203">HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="0559b-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="0559b-204">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="0559b-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0559b-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0559b-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="0559b-206">Windows Server 2016 / Windows 10 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="0559b-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="0559b-207">Целевая платформа: неприменимо к развертываниям HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0559b-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="0559b-208">IIS (внутрипроцессный)</span><span class="sxs-lookup"><span data-stu-id="0559b-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0559b-209">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="0559b-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0559b-210">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="0559b-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0559b-211">IIS (внепроцессный)</span><span class="sxs-lookup"><span data-stu-id="0559b-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0559b-212">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="0559b-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0559b-213">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к серверу Kestrel через обратный прокси-сервер — по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0559b-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="0559b-214">Целевая платформа: неприменимо к внепроцессным развертываниям IIS.</span><span class="sxs-lookup"><span data-stu-id="0559b-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="0559b-215">&dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="0559b-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0559b-216">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="0559b-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0559b-217">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="0559b-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="0559b-218">Kestrel</span><span class="sxs-lookup"><span data-stu-id="0559b-218">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="0559b-219">Операционная система</span><span class="sxs-lookup"><span data-stu-id="0559b-219">Operating system</span></span>
    * <span data-ttu-id="0559b-220">Windows Server 2016 / Windows 10 или более поздних версий&dagger;</span><span class="sxs-lookup"><span data-stu-id="0559b-220">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="0559b-221">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="0559b-221">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="0559b-222">HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="0559b-222">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="0559b-223">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="0559b-223">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0559b-224">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0559b-224">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="0559b-225">Windows Server 2016 / Windows 10 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="0559b-225">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="0559b-226">Целевая платформа: неприменимо к развертываниям HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0559b-226">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="0559b-227">IIS (внутрипроцессный)</span><span class="sxs-lookup"><span data-stu-id="0559b-227">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0559b-228">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="0559b-228">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0559b-229">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="0559b-229">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="0559b-230">IIS (внепроцессный)</span><span class="sxs-lookup"><span data-stu-id="0559b-230">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0559b-231">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="0559b-231">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0559b-232">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к серверу Kestrel через обратный прокси-сервер — по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0559b-232">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="0559b-233">Целевая платформа: неприменимо к внепроцессным развертываниям IIS.</span><span class="sxs-lookup"><span data-stu-id="0559b-233">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="0559b-234">&dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="0559b-234">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0559b-235">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="0559b-235">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0559b-236">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="0559b-236">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="0559b-237">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="0559b-237">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="0559b-238">Windows Server 2016 / Windows 10 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="0559b-238">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="0559b-239">Целевая платформа: неприменимо к развертываниям HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="0559b-239">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="0559b-240">IIS (внепроцессный)</span><span class="sxs-lookup"><span data-stu-id="0559b-240">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="0559b-241">Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="0559b-241">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="0559b-242">Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к серверу Kestrel через обратный прокси-сервер — по протоколу HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="0559b-242">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="0559b-243">Целевая платформа: неприменимо к внепроцессным развертываниям IIS.</span><span class="sxs-lookup"><span data-stu-id="0559b-243">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="0559b-244">Подключение HTTP/2 должно использовать [согласование протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) и TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="0559b-244">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="0559b-245">Дополнительные сведения см. в разделах, о конкретных сценариях развертывания сервера.</span><span class="sxs-lookup"><span data-stu-id="0559b-245">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0559b-246">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0559b-246">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
