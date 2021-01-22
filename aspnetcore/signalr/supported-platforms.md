---
title: Поддерживаемые платформы ASP.NET Core SignalR
author: bradygaster
description: Сведения о поддерживаемых платформах для ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689231"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core SignalR

## <a name="server-system-requirements"></a>Системные требования к  Server

SignalR для ASP.NET Core поддерживает любую серверную платформу, поддерживаемую ASP.NET Core.

## <a name="javascript-client"></a>Клиент JavaScript

[Клиент JavaScript](xref:signalr/javascript-client) работает на NodeJS 8 и более поздних версиях и в следующих браузерах:

| Браузер                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, включая iOS      | Текущий&dagger; |
| Google Chrome, включая Android | Текущий&dagger; |
| Microsoft Edge                   | Текущий&dagger; |
| Mozilla Firefox;                  | Текущий&dagger; |

&dagger;*Текущий* означает последнюю версию браузера.

Клиент JavaScript не поддерживает Internet Explorer и другие обозреватели предыдущих версий. Клиент может столкнуться с непредвиденным поведением и ошибками в неподдерживаемых браузерах.

## <a name="net-client"></a>Клиент .NET

[Клиент .NET](xref:signalr/dotnet-client) выполняется на любой платформе, поддерживаемой ASP.NET Core. Например, [разработчики Xamarin могут использовать SignalR ](https://github.com/aspnet/Announcements/issues/305) для создания приложений Android с помощью Xamarin. Android 8.4.0.1 и более поздних версий и приложений iOS с помощью Xamarin. iOS 11.14.0.4 и более поздних версий.

Если сервер использует службы IIS, для транспорта WebSocket требуется IIS 8,0 или более поздняя версия на Windows Server 2012 или более поздней версии. Другие транспорты поддерживаются на всех платформах.

## <a name="java-client"></a>Клиент Java

[Клиент Java](xref:signalr/java-client) поддерживает Java 8 и более поздних версий.

## <a name="unsupported-clients"></a>Неподдерживаемые клиенты

Следующие клиенты доступны, но являются экспериментальными или неофициальными. В настоящее время они не поддерживаются и никогда не могут быть.

* [Клиент C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Клиент SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
