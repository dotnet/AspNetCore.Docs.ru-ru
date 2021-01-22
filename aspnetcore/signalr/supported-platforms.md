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
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="265b2-103">Поддерживаемые платформы ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="265b2-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="265b2-104">Системные требования к  Server</span><span class="sxs-lookup"><span data-stu-id="265b2-104">Server system requirements</span></span>

<span data-ttu-id="265b2-105">SignalR для ASP.NET Core поддерживает любую серверную платформу, поддерживаемую ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="265b2-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="265b2-106">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="265b2-106">JavaScript client</span></span>

<span data-ttu-id="265b2-107">[Клиент JavaScript](xref:signalr/javascript-client) работает на NodeJS 8 и более поздних версиях и в следующих браузерах:</span><span class="sxs-lookup"><span data-stu-id="265b2-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="265b2-108">Браузер</span><span class="sxs-lookup"><span data-stu-id="265b2-108">Browser</span></span>                          | <span data-ttu-id="265b2-109">Version</span><span class="sxs-lookup"><span data-stu-id="265b2-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="265b2-110">Apple Safari, включая iOS</span><span class="sxs-lookup"><span data-stu-id="265b2-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="265b2-111">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="265b2-111">Current&dagger;</span></span> |
| <span data-ttu-id="265b2-112">Google Chrome, включая Android</span><span class="sxs-lookup"><span data-stu-id="265b2-112">Google Chrome, including Android</span></span> | <span data-ttu-id="265b2-113">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="265b2-113">Current&dagger;</span></span> |
| <span data-ttu-id="265b2-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="265b2-114">Microsoft Edge</span></span>                   | <span data-ttu-id="265b2-115">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="265b2-115">Current&dagger;</span></span> |
| <span data-ttu-id="265b2-116">Mozilla Firefox;</span><span class="sxs-lookup"><span data-stu-id="265b2-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="265b2-117">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="265b2-117">Current&dagger;</span></span> |

<span data-ttu-id="265b2-118">&dagger;*Текущий* означает последнюю версию браузера.</span><span class="sxs-lookup"><span data-stu-id="265b2-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

<span data-ttu-id="265b2-119">Клиент JavaScript не поддерживает Internet Explorer и другие обозреватели предыдущих версий.</span><span class="sxs-lookup"><span data-stu-id="265b2-119">The JavaScript client doesn't support Internet Explorer and other older browsers.</span></span> <span data-ttu-id="265b2-120">Клиент может столкнуться с непредвиденным поведением и ошибками в неподдерживаемых браузерах.</span><span class="sxs-lookup"><span data-stu-id="265b2-120">The client might have unexpected behavior and errors on unsupported browsers.</span></span>

## <a name="net-client"></a><span data-ttu-id="265b2-121">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="265b2-121">.NET client</span></span>

<span data-ttu-id="265b2-122">[Клиент .NET](xref:signalr/dotnet-client) выполняется на любой платформе, поддерживаемой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="265b2-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="265b2-123">Например, [разработчики Xamarin могут использовать SignalR ](https://github.com/aspnet/Announcements/issues/305) для создания приложений Android с помощью Xamarin. Android 8.4.0.1 и более поздних версий и приложений iOS с помощью Xamarin. iOS 11.14.0.4 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="265b2-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="265b2-124">Если сервер использует службы IIS, для транспорта WebSocket требуется IIS 8,0 или более поздняя версия на Windows Server 2012 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="265b2-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="265b2-125">Другие транспорты поддерживаются на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="265b2-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="265b2-126">Клиент Java</span><span class="sxs-lookup"><span data-stu-id="265b2-126">Java client</span></span>

<span data-ttu-id="265b2-127">[Клиент Java](xref:signalr/java-client) поддерживает Java 8 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="265b2-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="265b2-128">Неподдерживаемые клиенты</span><span class="sxs-lookup"><span data-stu-id="265b2-128">Unsupported clients</span></span>

<span data-ttu-id="265b2-129">Следующие клиенты доступны, но являются экспериментальными или неофициальными.</span><span class="sxs-lookup"><span data-stu-id="265b2-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="265b2-130">В настоящее время они не поддерживаются и никогда не могут быть.</span><span class="sxs-lookup"><span data-stu-id="265b2-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="265b2-131">[Клиент C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="265b2-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="265b2-132">[Клиент SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="265b2-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
