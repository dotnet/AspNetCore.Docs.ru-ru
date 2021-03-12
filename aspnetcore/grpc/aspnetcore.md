---
title: Службы gRPC в ASP.NET Core
author: juntaoluo
description: Ознакомьтесь с основными понятиями при написании служб gRPC с помощью ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: aeeb3d23adbdebc35ea2d2671fb04dea57451b5b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588988"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="bffc7-103">Службы gRPC в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bffc7-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="bffc7-104">В этом документе показано, как приступить к работе со службами gRPC с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="bffc7-105">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="bffc7-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bffc7-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bffc7-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bffc7-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bffc7-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bffc7-108">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="bffc7-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="bffc7-109">Начало работы со службой gRPC в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bffc7-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="bffc7-110">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bffc7-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bffc7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bffc7-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bffc7-112">Подробные инструкции по созданию проекта gRPC см. в статье [Начало работы со службами gRPC](xref:tutorials/grpc/grpc-start).</span><span class="sxs-lookup"><span data-stu-id="bffc7-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bffc7-113">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="bffc7-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bffc7-114">Из командной строки выполните команду `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="bffc7-115">Добавление служб gRPC в приложение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bffc7-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="bffc7-116">Для gRPC требуется пакет [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="bffc7-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="bffc7-117">Настройка gRPC</span><span class="sxs-lookup"><span data-stu-id="bffc7-117">Configure gRPC</span></span>

<span data-ttu-id="bffc7-118">В файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bffc7-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="bffc7-119">gRPC включается с помощью метода `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="bffc7-120">Каждая служба gRPC добавляется в конвейер маршрутизации с помощью метода `MapGrpcService`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="bffc7-121">ПО промежуточного слоя ASP.NET Core и компоненты совместно используют конвейер маршрутов, поэтому приложение можно настроить для обслуживания дополнительных обработчиков запросов.</span><span class="sxs-lookup"><span data-stu-id="bffc7-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="bffc7-122">Дополнительные обработчики запросов, такие как контроллеры MVC, работают параллельно с настроенными службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="bffc7-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="bffc7-123">Параметры сервера</span><span class="sxs-lookup"><span data-stu-id="bffc7-123">Server options</span></span>

<span data-ttu-id="bffc7-124">Службы gRPC могут размещаться всеми встроенными серверами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="bffc7-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="bffc7-125">Kestrel</span></span>
> * <span data-ttu-id="bffc7-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="bffc7-126">TestServer</span></span>
> * <span data-ttu-id="bffc7-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="bffc7-127">IIS&dagger;</span></span>
> * <span data-ttu-id="bffc7-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="bffc7-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="bffc7-129">&dagger;Для работы служб IIS требуется .NET 5 и Windows 10 сборки 20241 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="bffc7-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="bffc7-130">&Dagger;Для работы HTTP.sys требуется .NET 5 и Windows 10 сборки 19529 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="bffc7-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="bffc7-131">Дополнительные сведения о выборе правильного сервера для приложения ASP.NET Core см. в статье <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="bffc7-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="bffc7-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="bffc7-132">Kestrel</span></span>

<span data-ttu-id="bffc7-133">[Kestrel](xref:fundamentals/servers/kestrel) — это кроссплатформенный веб-сервер для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="bffc7-134">Kestrel обеспечивает максимальную производительность и эффективное использование памяти, однако в ней нет некоторых дополнительных функций в HTTP.sys, например общего доступа к портам.</span><span class="sxs-lookup"><span data-stu-id="bffc7-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="bffc7-135">Конечные точки gRPC Kestrel:</span><span class="sxs-lookup"><span data-stu-id="bffc7-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="bffc7-136">Требуется HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-136">Require HTTP/2.</span></span>
* <span data-ttu-id="bffc7-137">Безопасность следует обеспечивать с помощью [протокола TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="bffc7-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="bffc7-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="bffc7-138">HTTP/2</span></span>

<span data-ttu-id="bffc7-139">Для gRPC требуется HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="bffc7-140">gRPC для ASP.NET Core проверяет, что [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) имеет значение `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="bffc7-141">Kestrel [поддерживает HTTP/2](xref:fundamentals/servers/kestrel/http2) в большинстве современных операционных систем.</span><span class="sxs-lookup"><span data-stu-id="bffc7-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="bffc7-142">Конечные точки Kestrel настроены для поддержки подключений HTTP/1.1 и HTTP/2 по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="bffc7-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="bffc7-143">TLS</span><span class="sxs-lookup"><span data-stu-id="bffc7-143">TLS</span></span>

<span data-ttu-id="bffc7-144">Конечные точки Kestrel, используемые для gRPC, должны быть защищены с помощью TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="bffc7-145">При разработке конечная точка, защищенная с помощью TLS, автоматически создается в `https://localhost:5001` при наличии сертификата разработки ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="bffc7-146">Настройка не требуется.</span><span class="sxs-lookup"><span data-stu-id="bffc7-146">No configuration is required.</span></span> <span data-ttu-id="bffc7-147">Префикс `https` проверяет, что конечная точка Kestrel использует TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="bffc7-148">В рабочей среде необходимо явно настроить TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="bffc7-149">В следующем примере *appsettings.json* предоставляется конечная точка HTTP/2, защищенная с помощью TLS:</span><span class="sxs-lookup"><span data-stu-id="bffc7-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="bffc7-150">Кроме того, конечные точки Kestrel можно настроить в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bffc7-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="bffc7-151">Согласование протокола</span><span class="sxs-lookup"><span data-stu-id="bffc7-151">Protocol negotiation</span></span>

<span data-ttu-id="bffc7-152">Протокол TLS используется не только для защиты обмена данными.</span><span class="sxs-lookup"><span data-stu-id="bffc7-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="bffc7-153">Подтверждение установки связи TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) используется для согласования протокола подключения между клиентом и сервером, если конечная точка поддерживает несколько протоколов.</span><span class="sxs-lookup"><span data-stu-id="bffc7-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="bffc7-154">Это согласование определяет, использует ли соединение HTTP/1.1 или HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="bffc7-155">Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="bffc7-156">Конечная точка с несколькими протоколами (например, `HttpProtocols.Http1AndHttp2`) не может использоваться без TLS, так как отсутствует согласование.</span><span class="sxs-lookup"><span data-stu-id="bffc7-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="bffc7-157">Все подключения к незащищенной конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.</span><span class="sxs-lookup"><span data-stu-id="bffc7-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="bffc7-158">Дополнительные сведения о включении HTTP/2 и TLS с помощью Kestrel см. в разделе [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="bffc7-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="bffc7-159">macOS не поддерживает ASP.NET Core gRPC с TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="bffc7-160">Для успешного запуска служб gRPC в macOS требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="bffc7-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="bffc7-161">Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="bffc7-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="bffc7-162">IIS</span><span class="sxs-lookup"><span data-stu-id="bffc7-162">IIS</span></span>

<span data-ttu-id="bffc7-163">[Службы IIS](xref:host-and-deploy/iis/index) — это гибкий, безопасный и управляемый веб-сервер для размещения веб-приложений, включая ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="bffc7-164">Для размещения служб gRPC со службами IIS требуются .NET 5 и Windows 10 сборки 20241 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="bffc7-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="bffc7-165">Службы IIS должны быть настроены для использования TLS и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="bffc7-166">Для получения дополнительной информации см. <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="bffc7-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="bffc7-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="bffc7-167">HTTP.sys</span></span>

<span data-ttu-id="bffc7-168">[HTTP.sys](xref:fundamentals/servers/httpsys) — это веб-сервер для ASP.NET Core, который запускается только в Windows.</span><span class="sxs-lookup"><span data-stu-id="bffc7-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="bffc7-169">Для размещения служб gRPC с HTTP.sys. требуются .NET 5 и Windows 10 сборки 19529 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="bffc7-169">.NET 5 and Windows 10 Build 19529 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="bffc7-170">HTTP.sys необходимо настроить для использования TLS и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="bffc7-171">Дополнительные сведения см. в разделе [Поддержка HTTP/2 для веб-сервера HTTP.sys](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="bffc7-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="bffc7-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="bffc7-172">Kestrel</span></span>

<span data-ttu-id="bffc7-173">[Kestrel](xref:fundamentals/servers/kestrel) — это кроссплатформенный веб-сервер для ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="bffc7-174">Kestrel обеспечивает максимальную производительность и эффективное использование памяти, однако в ней нет некоторых дополнительных функций в HTTP.sys, например общего доступа к портам.</span><span class="sxs-lookup"><span data-stu-id="bffc7-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="bffc7-175">Конечные точки gRPC Kestrel:</span><span class="sxs-lookup"><span data-stu-id="bffc7-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="bffc7-176">Требуется HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-176">Require HTTP/2.</span></span>
* <span data-ttu-id="bffc7-177">Безопасность следует обеспечивать с помощью [протокола TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="bffc7-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="bffc7-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="bffc7-178">HTTP/2</span></span>

<span data-ttu-id="bffc7-179">Для gRPC требуется HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="bffc7-180">gRPC для ASP.NET Core проверяет, что [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) имеет значение `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="bffc7-181">Kestrel [поддерживает HTTP/2](xref:fundamentals/servers/kestrel#http2-support) в большинстве современных операционных систем.</span><span class="sxs-lookup"><span data-stu-id="bffc7-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="bffc7-182">Конечные точки Kestrel настроены для поддержки подключений HTTP/1.1 и HTTP/2 по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="bffc7-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="bffc7-183">TLS</span><span class="sxs-lookup"><span data-stu-id="bffc7-183">TLS</span></span>

<span data-ttu-id="bffc7-184">Конечные точки Kestrel, используемые для gRPC, должны быть защищены с помощью TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="bffc7-185">При разработке конечная точка, защищенная с помощью TLS, автоматически создается в `https://localhost:5001` при наличии сертификата разработки ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bffc7-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="bffc7-186">Настройка не требуется.</span><span class="sxs-lookup"><span data-stu-id="bffc7-186">No configuration is required.</span></span> <span data-ttu-id="bffc7-187">Префикс `https` проверяет, что конечная точка Kestrel использует TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="bffc7-188">В рабочей среде необходимо явно настроить TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="bffc7-189">В следующем примере *appsettings.json* предоставляется конечная точка HTTP/2, защищенная с помощью TLS:</span><span class="sxs-lookup"><span data-stu-id="bffc7-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="bffc7-190">Кроме того, конечные точки Kestrel можно настроить в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bffc7-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="bffc7-191">Согласование протокола</span><span class="sxs-lookup"><span data-stu-id="bffc7-191">Protocol negotiation</span></span>

<span data-ttu-id="bffc7-192">Протокол TLS используется не только для защиты обмена данными.</span><span class="sxs-lookup"><span data-stu-id="bffc7-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="bffc7-193">Подтверждение установки связи TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) используется для согласования протокола подключения между клиентом и сервером, если конечная точка поддерживает несколько протоколов.</span><span class="sxs-lookup"><span data-stu-id="bffc7-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="bffc7-194">Это согласование определяет, использует ли соединение HTTP/1.1 или HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="bffc7-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="bffc7-195">Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="bffc7-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="bffc7-196">Конечная точка с несколькими протоколами (например, `HttpProtocols.Http1AndHttp2`) не может использоваться без TLS, так как отсутствует согласование.</span><span class="sxs-lookup"><span data-stu-id="bffc7-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="bffc7-197">Все подключения к незащищенной конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.</span><span class="sxs-lookup"><span data-stu-id="bffc7-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="bffc7-198">Дополнительные сведения о включении HTTP/2 и TLS с помощью Kestrel см. в разделе [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="bffc7-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="bffc7-199">macOS не поддерживает ASP.NET Core gRPC с TLS.</span><span class="sxs-lookup"><span data-stu-id="bffc7-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="bffc7-200">Для успешного запуска служб gRPC в macOS требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="bffc7-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="bffc7-201">Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="bffc7-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="bffc7-202">Интеграция с API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bffc7-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="bffc7-203">Службы gRPC имеют полный доступ к компонентам ASP.NET Core, таким как [внедрение зависимостей](xref:fundamentals/dependency-injection) (DI) и [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="bffc7-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="bffc7-204">Например, реализация службы может разрешить службу ведения журнала из контейнера DI с помощью конструктора:</span><span class="sxs-lookup"><span data-stu-id="bffc7-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="bffc7-205">По умолчанию реализация службы gRPC может разрешать другие службы DI с любым временем существования (отдельное, ограниченное, временное).</span><span class="sxs-lookup"><span data-stu-id="bffc7-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="bffc7-206">Разрешение HttpContext в методах gRPC</span><span class="sxs-lookup"><span data-stu-id="bffc7-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="bffc7-207">API gRPC предоставляет доступ к некоторым данным сообщений HTTP/2, таким как метод, узел, заголовок и трейлеры.</span><span class="sxs-lookup"><span data-stu-id="bffc7-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="bffc7-208">Доступ осуществляется через аргумент `ServerCallContext`, передаваемый в каждый метод gRPC:</span><span class="sxs-lookup"><span data-stu-id="bffc7-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="bffc7-209">`ServerCallContext` не предоставляет полный доступ к `HttpContext` во всех API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="bffc7-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="bffc7-210">Метод расширения `GetHttpContext` предоставляет полный доступ к `HttpContext`, представляющему базовое сообщение HTTP/2 в API ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="bffc7-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="bffc7-211">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="bffc7-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
