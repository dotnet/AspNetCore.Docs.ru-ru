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
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Использование HTTP/2 с веб-сервером Kestrel для ASP.NET Core

Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) доступен для приложений ASP.NET Core, если выполнены следующие базовые требования:

* Операционная система&dagger;:
  * Windows Server 2016 / Windows 10 или более поздних версий&Dagger;
  * Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).
* Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней
* Подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3).
* Подключение TLS 1.2 или более поздней версии.

&dagger; HTTP/2 будет поддерживаться для macOS в будущих выпусках.
&Dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1. Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем. Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.

Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) возвращает `HTTP/2`.

Начиная с версии .NET Core 3.0 протокол HTTP/2 по умолчанию включен. Дополнительные сведения о конфигурации см. в разделах, посвященных [ограничениям для HTTP/2 в Kestrel](xref:fundamentals/servers/kestrel/options#http2-limits) и [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols).

## <a name="advanced-http2-features"></a>Расширенные возможности HTTP/2

Дополнительные возможности HTTP/2 в службах Kestrel обеспечивают поддержку gRPC, включая трейлеры ответов и отправку кадров сброса.

### <a name="trailers"></a>Трейлеры

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
