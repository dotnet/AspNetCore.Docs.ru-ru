---
title: Использование gRPC на поддерживаемых платформах .NET
author: jamesnk
description: Сведения о поддерживаемых платформах для gRPC в .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: 6e48a19027f79b75edeebde9c584419871fba533
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530168"
---
# <a name="grpc-on-net-supported-platforms"></a><span data-ttu-id="7caf2-103">Использование gRPC на поддерживаемых платформах .NET</span><span class="sxs-lookup"><span data-stu-id="7caf2-103">gRPC on .NET supported platforms</span></span>

<span data-ttu-id="7caf2-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="7caf2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7caf2-105">В этой статье описаны требования и поддерживаемые платформы для использования gRPC с .NET.</span><span class="sxs-lookup"><span data-stu-id="7caf2-105">This article discusses the requirements and supported platforms for using gRPC with .NET.</span></span>

<span data-ttu-id="7caf2-106">gRPC использует преимущества расширенных функций, предоставляемых HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7caf2-106">gRPC takes advantage of advanced features available in  HTTP/2.</span></span> <span data-ttu-id="7caf2-107">HTTP/2 поддерживается везде, но для gRPC доступен второй сетевой формат на основе HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="7caf2-107">HTTP/2 isn't supported everywhere, but a second wire-format using HTTP/1.1 is available for gRPC:</span></span>

* <span data-ttu-id="7caf2-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) — обычное использование gRPC через HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7caf2-108">[`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) - gRPC over HTTP/2 is how gRPC is typically used.</span></span>
* <span data-ttu-id="7caf2-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) — gRPC-Web изменяет протокол gRPC, обеспечивая совместимость с HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="7caf2-109">[`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) - gRPC-Web modifies the gRPC protocol to be compatible with HTTP/1.1.</span></span> <span data-ttu-id="7caf2-110">gRPC-Web можно использовать в других сценариях, в частности при вызове браузерными приложениями.</span><span class="sxs-lookup"><span data-stu-id="7caf2-110">gRPC-Web can be used in more places, notably it is callable by browser apps.</span></span> <span data-ttu-id="7caf2-111">Две расширенные функции gRPC больше не поддерживаются: потоковая передача клиента и двунаправленная потоковая передача.</span><span class="sxs-lookup"><span data-stu-id="7caf2-111">Two advanced gRPC features are no longer supported: client streaming and bidirectional streaming.</span></span>

<span data-ttu-id="7caf2-112">gRPC в .NET поддерживает оба формата для передачи данных по сети.</span><span class="sxs-lookup"><span data-stu-id="7caf2-112">gRPC on .NET supports both wire-formats.</span></span> <span data-ttu-id="7caf2-113">gRPC через HTTP/2 используется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7caf2-113">gRPC over HTTP/2 is used by default.</span></span> <span data-ttu-id="7caf2-114">Сведения о настройке gRPC-Web см. здесь: <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="7caf2-114">For information on setting up gRPC-Web, see <xref:grpc/browser>.</span></span>

## <a name="device-requirements"></a><span data-ttu-id="7caf2-115">Требования к устройствам</span><span class="sxs-lookup"><span data-stu-id="7caf2-115">Device requirements</span></span>

<span data-ttu-id="7caf2-116">gRPC в .NET поддерживает любое устройство, поддерживаемое .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7caf2-116">gRPC on .NET supports any device that .NET Core supports.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="7caf2-117">Windows</span><span class="sxs-lookup"><span data-stu-id="7caf2-117">Windows</span></span>
> * <span data-ttu-id="7caf2-118">Linux</span><span class="sxs-lookup"><span data-stu-id="7caf2-118">Linux</span></span>
> * <span data-ttu-id="7caf2-119">macOS&dagger;</span><span class="sxs-lookup"><span data-stu-id="7caf2-119">macOS&dagger;</span></span>
> * <span data-ttu-id="7caf2-120">Браузеры&Dagger;</span><span class="sxs-lookup"><span data-stu-id="7caf2-120">Browsers&Dagger;</span></span>

<span data-ttu-id="7caf2-121">&dagger;[macOS не поддерживает размещение приложений ASP.NET Core с использованием HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="7caf2-121">&dagger;[macOS doesn't support hosting ASP.NET Core apps with HTTPS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span> <span data-ttu-id="7caf2-122">Клиенты gRPC в macOS могут вызывать удаленные службы, использующие HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7caf2-122">gRPC clients on macOS can call remote services that use HTTPS.</span></span>

<span data-ttu-id="7caf2-123">Приложения Blazor WebAssembly&Dagger; могут вызывать службы gRPC с использованием gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="7caf2-123">&Dagger;Blazor WebAssembly apps can call gRPC services with gRPC-Web.</span></span>

## <a name="aspnet-core-server-requirements"></a><span data-ttu-id="7caf2-124">Требования к серверу ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7caf2-124">ASP.NET Core server requirements</span></span>

<span data-ttu-id="7caf2-125">Службы gRPC могут размещаться на всех встроенных серверах ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7caf2-125">gRPC services can be hosted on all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="7caf2-126">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7caf2-126">Kestrel</span></span>
> * <span data-ttu-id="7caf2-127">TestServer</span><span class="sxs-lookup"><span data-stu-id="7caf2-127">TestServer</span></span>
> * <span data-ttu-id="7caf2-128">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="7caf2-128">IIS&dagger;</span></span>
> * <span data-ttu-id="7caf2-129">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="7caf2-129">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="7caf2-130">&dagger;Для работы служб IIS требуется .NET 5 и Windows 10 сборки 20241 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="7caf2-130">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="7caf2-131">&Dagger;Для работы HTTP.sys требуется .NET 5 и Windows 10 сборки 19529 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="7caf2-131">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="7caf2-132">Сведения о настройке серверов ASP.NET Core для запуска gRPC см. здесь: <xref:grpc/aspnetcore#server-options>.</span><span class="sxs-lookup"><span data-stu-id="7caf2-132">For information about configuring ASP.NET Core servers to run gRPC, see <xref:grpc/aspnetcore#server-options>.</span></span>

## <a name="net-version-requirements"></a><span data-ttu-id="7caf2-133">Требования к версии .NET</span><span class="sxs-lookup"><span data-stu-id="7caf2-133">.NET version requirements</span></span>

<span data-ttu-id="7caf2-134">gRPC в .NET поддерживает .NET Core 3 и .NET 5 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="7caf2-134">gRPC on .NET supports .NET Core 3 and .NET 5 or later.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="7caf2-135">.NET 5 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="7caf2-135">.NET 5 or later</span></span>
> * <span data-ttu-id="7caf2-136">.NET Core 3</span><span class="sxs-lookup"><span data-stu-id="7caf2-136">.NET Core 3</span></span>

<span data-ttu-id="7caf2-137">gRPC в .NET не поддерживает выполнение в .NET Framework и Xamarin.</span><span class="sxs-lookup"><span data-stu-id="7caf2-137">gRPC on .NET doesn't support running on .NET Framework and Xamarin.</span></span> <span data-ttu-id="7caf2-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) — это сторонняя библиотека, которая поддерживает .NET Framework and Xamarin.</span><span class="sxs-lookup"><span data-stu-id="7caf2-138">[gRPC C# core-library](https://grpc.io/docs/languages/csharp/quickstart/) is a third party library that supports .NET Framework and Xamarin.</span></span> <span data-ttu-id="7caf2-139">gRPC C-core не поддерживается Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="7caf2-139">gRPC C-core is not supported by Microsoft.</span></span>

## <a name="azure-services"></a><span data-ttu-id="7caf2-140">Службы Azure</span><span class="sxs-lookup"><span data-stu-id="7caf2-140">Azure services</span></span>

> [!div class="checklist"]
>
> * [<span data-ttu-id="7caf2-141">Служба Azure Kubernetes (AKS)</span><span class="sxs-lookup"><span data-stu-id="7caf2-141">Azure Kubernetes Service (AKS)</span></span>](https://azure.microsoft.com/services/kubernetes-service/)
> * <span data-ttu-id="7caf2-142">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/)&dagger;</span><span class="sxs-lookup"><span data-stu-id="7caf2-142">[Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;</span></span>

<span data-ttu-id="7caf2-143">&dagger;Служба приложений Azure не поддерживает размещение gRPC по протоколу HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7caf2-143">&dagger;Azure App Service doesn't support hosting gRPC over HTTP/2.</span></span> <span data-ttu-id="7caf2-144">gRPC-Web является совместимой альтернативой.</span><span class="sxs-lookup"><span data-stu-id="7caf2-144">gRPC-Web is a compatible alternative.</span></span>

<span data-ttu-id="7caf2-145">Мы работаем над улучшением поддержки gRPC с HTTP/2 в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="7caf2-145">Work is in-progress to improve support for gRPC with HTTP/2 in Azure App Service.</span></span> <span data-ttu-id="7caf2-146">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore/issues/9020).</span><span class="sxs-lookup"><span data-stu-id="7caf2-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/9020).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7caf2-147">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7caf2-147">Additional resources</span></span>

* [<span data-ttu-id="7caf2-148">gRPC C# core-library</span><span class="sxs-lookup"><span data-stu-id="7caf2-148">gRPC C# core-library</span></span>](https://grpc.io/docs/languages/csharp/quickstart/)
