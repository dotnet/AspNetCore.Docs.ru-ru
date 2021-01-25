---
title: Когда следует использовать обратный прокси-сервер с веб-сервером Kestrel для ASP.NET Core
author: rick-anderson
description: Дополнительные сведения о том, когда следует использовать обратный прокси-сервер перед Kestrel, кроссплатформенным веб-сервером для ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253856"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="320b2-103">Условия использования Kestrel с обратным прокси-сервером</span><span class="sxs-lookup"><span data-stu-id="320b2-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="320b2-104">Kestrel можно использовать отдельно или с *обратным прокси-сервером*, таким как [IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="320b2-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="320b2-105">Обратный прокси-сервер получает HTTP-запросы из сети и пересылает их в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="320b2-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="320b2-106">Kestrel используется в качестве веб-сервера перехода (с выходом в Интернет).</span><span class="sxs-lookup"><span data-stu-id="320b2-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](_static/kestrel-to-internet2.png)

<span data-ttu-id="320b2-108">Kestrel используется в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="320b2-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](_static/kestrel-to-internet.png)

<span data-ttu-id="320b2-110">Любая из этих конфигураций — с обратным прокси-сервером и без него — является поддерживаемой конфигурацией для размещения.</span><span class="sxs-lookup"><span data-stu-id="320b2-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="320b2-111">Kestrel, используемый в качестве пограничного сервера без обратного прокси-сервера, не поддерживает общий доступ нескольких процессов к одним и тем же IP-адресам и портам.</span><span class="sxs-lookup"><span data-stu-id="320b2-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="320b2-112">Когда Kestrel настроен на ожидание передачи данных от порта, Kestrel обрабатывает весь трафик для этого порта независимо от заголовка `Host` запросов.</span><span class="sxs-lookup"><span data-stu-id="320b2-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="320b2-113">Поэтому обратный прокси-сервер, который может совместно использовать порты, способен пересылать запросы в Kestrel с уникальным IP-адресом и портом.</span><span class="sxs-lookup"><span data-stu-id="320b2-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="320b2-114">Даже если обратный прокси-сервер не требуется, его использование может оказаться удобным.</span><span class="sxs-lookup"><span data-stu-id="320b2-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="320b2-115">Обратный прокси-сервер:</span><span class="sxs-lookup"><span data-stu-id="320b2-115">A reverse proxy:</span></span>

* <span data-ttu-id="320b2-116">Может ограничить общедоступную контактную зону размещенных на нем приложений.</span><span class="sxs-lookup"><span data-stu-id="320b2-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="320b2-117">Предоставляет дополнительный уровень конфигурации и защиты.</span><span class="sxs-lookup"><span data-stu-id="320b2-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="320b2-118">Может лучше интегрироваться с существующей инфраструктурой.</span><span class="sxs-lookup"><span data-stu-id="320b2-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="320b2-119">Упрощает настройку балансировки нагрузки и безопасных подключений (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="320b2-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="320b2-120">Сертификат X.509 требуется только обратному прокси-серверу, а сам этот сервер может обмениваться данными с серверами приложения во внутренней сети по обычному протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="320b2-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="320b2-121">Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="320b2-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="320b2-122">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="320b2-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

