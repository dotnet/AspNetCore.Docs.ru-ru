---
title: Использование HTTP/2 с веб-сервером Kestrel для ASP.NET Core
author: rick-anderson
description: Сведения об использовании HTTP/2 с Kestrel, кроссплатформенным веб-сервером для ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253894"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="aadb2-103">Использование HTTP/2 с веб-сервером Kestrel для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aadb2-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="aadb2-104">Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) доступен для приложений ASP.NET Core, если выполнены следующие базовые требования:</span><span class="sxs-lookup"><span data-stu-id="aadb2-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="aadb2-105">Операционная система&dagger;:</span><span class="sxs-lookup"><span data-stu-id="aadb2-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="aadb2-106">Windows Server 2016 / Windows 10 или более поздних версий&Dagger;</span><span class="sxs-lookup"><span data-stu-id="aadb2-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="aadb2-107">Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="aadb2-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="aadb2-108">Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней</span><span class="sxs-lookup"><span data-stu-id="aadb2-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="aadb2-109">Подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3).</span><span class="sxs-lookup"><span data-stu-id="aadb2-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="aadb2-110">Подключение TLS 1.2 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="aadb2-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="aadb2-111">&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="aadb2-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="aadb2-112">&Dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="aadb2-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="aadb2-113">Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем.</span><span class="sxs-lookup"><span data-stu-id="aadb2-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="aadb2-114">Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.</span><span class="sxs-lookup"><span data-stu-id="aadb2-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="aadb2-115">Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) возвращает `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="aadb2-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="aadb2-116">Начиная с версии .NET Core 3.0 протокол HTTP/2 по умолчанию включен.</span><span class="sxs-lookup"><span data-stu-id="aadb2-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="aadb2-117">Дополнительные сведения о конфигурации см. в разделах, посвященных [ограничениям для HTTP/2 в Kestrel](xref:fundamentals/servers/kestrel/options#http2-limits) и [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="aadb2-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="aadb2-118">Расширенные возможности HTTP/2</span><span class="sxs-lookup"><span data-stu-id="aadb2-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="aadb2-119">Дополнительные возможности HTTP/2 в службах Kestrel обеспечивают поддержку gRPC, включая трейлеры ответов и отправку кадров сброса.</span><span class="sxs-lookup"><span data-stu-id="aadb2-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="aadb2-120">Трейлеры</span><span class="sxs-lookup"><span data-stu-id="aadb2-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="aadb2-121">Reset</span><span class="sxs-lookup"><span data-stu-id="aadb2-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
