---
title: Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки
author: rick-anderson
description: Сведения о конфигурации приложений, размещаемых за прокси-серверами и подсистемами балансировки нагрузки, которые могут мешать передаче важных сведений в запросах.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: e63821743dad565b51d5c2360dcc2fbf0632754f
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530051"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="58088-103">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="58088-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="58088-104">Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="58088-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58088-105">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="58088-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="58088-106">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="58088-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="58088-107">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="58088-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="58088-108">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="58088-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="58088-109">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="58088-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="58088-110">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="58088-110">Forwarded headers</span></span>

<span data-ttu-id="58088-111">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="58088-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="58088-112">Header</span><span class="sxs-lookup"><span data-stu-id="58088-112">Header</span></span> | <span data-ttu-id="58088-113">Описание</span><span class="sxs-lookup"><span data-stu-id="58088-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="58088-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="58088-114">X-Forwarded-For</span></span> | <span data-ttu-id="58088-115">Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="58088-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="58088-116">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="58088-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="58088-117">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="58088-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="58088-118">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="58088-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="58088-119">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="58088-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="58088-120">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="58088-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="58088-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="58088-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="58088-122">Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="58088-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="58088-123">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="58088-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="58088-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="58088-124">X-Forwarded-Host</span></span> | <span data-ttu-id="58088-125">Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="58088-125">The original value of the Host header field.</span></span> <span data-ttu-id="58088-126">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="58088-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="58088-127">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="58088-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="58088-128">ПО промежуточного слоя перенаправления заголовков (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="58088-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="58088-129">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="58088-129">The middleware updates:</span></span>

* <span data-ttu-id="58088-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) — задайте с помощью значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="58088-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="58088-131">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="58088-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="58088-132">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="58088-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) — задайте с помощью значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="58088-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) — задайте с помощью значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="58088-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="58088-135">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="58088-136">По умолчанию используются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="58088-136">The default settings are:</span></span>

* <span data-ttu-id="58088-137">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="58088-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="58088-138">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="58088-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="58088-139">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="58088-140">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="58088-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="58088-141">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="58088-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="58088-142">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="58088-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="58088-143">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="58088-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="58088-144">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58088-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="58088-145">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58088-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="58088-146">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="58088-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="58088-147">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="58088-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="58088-148">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="58088-149">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="58088-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="58088-150">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="58088-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="58088-151">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="58088-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="58088-152">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="58088-153">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="58088-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="58088-154">Порядок ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="58088-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="58088-155">ПО промежуточного слоя перенаправления заголовков должно выполняться до другого ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="58088-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="58088-156">Такой порядок гарантирует, что ПО промежуточного слоя, полагающееся на сведения о перенаправленных заголовках, может использовать значения заголовков для обработки.</span><span class="sxs-lookup"><span data-stu-id="58088-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="58088-157">ПО промежуточного слоя перенаправления заголовков может выполняться после диагностики и обработки ошибок, однако его необходимо выполнить перед вызовом `UseHsts`.</span><span class="sxs-lookup"><span data-stu-id="58088-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="58088-158">Кроме того, можно вызвать `UseForwardedHeaders` перед диагностикой:</span><span class="sxs-lookup"><span data-stu-id="58088-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="58088-159">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="58088-160">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="58088-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="58088-161">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="58088-161">Nginx configuration</span></span>

<span data-ttu-id="58088-162">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="58088-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="58088-163">Дополнительные сведения см. в статье об [использовании заголовка Forwarded в NGINX](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="58088-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="58088-164">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="58088-164">Apache configuration</span></span>

<span data-ttu-id="58088-165">`X-Forwarded-For` добавляется автоматически (см. документацию по [заголовкам обратных прокси-запросов в модуле Apache mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="58088-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="58088-166">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="58088-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="58088-167">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="58088-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="58088-168">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="58088-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="58088-169">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="58088-169">The following example changes the default values:</span></span>

* <span data-ttu-id="58088-170">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="58088-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="58088-171">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="58088-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="58088-172">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="58088-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="58088-173">Параметр</span><span class="sxs-lookup"><span data-stu-id="58088-173">Option</span></span> | <span data-ttu-id="58088-174">Описание</span><span class="sxs-lookup"><span data-stu-id="58088-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="58088-175">Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="58088-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="58088-176">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="58088-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="58088-177">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="58088-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="58088-178">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="58088-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="58088-179">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="58088-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="58088-180">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="58088-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="58088-181">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="58088-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="58088-182">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="58088-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="58088-183">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="58088-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="58088-184">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="58088-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="58088-185">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="58088-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="58088-186">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="58088-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="58088-187">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="58088-188">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="58088-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="58088-189">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="58088-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="58088-190">Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="58088-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="58088-191">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="58088-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="58088-192">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="58088-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="58088-193">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="58088-194">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="58088-195">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="58088-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="58088-196">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="58088-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="58088-197">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="58088-198">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="58088-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="58088-199">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="58088-200">Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="58088-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="58088-201">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="58088-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="58088-202">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="58088-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="58088-203">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="58088-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="58088-204">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="58088-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="58088-205">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="58088-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="58088-206">Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="58088-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="58088-207">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="58088-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="58088-208">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="58088-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="58088-209">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="58088-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="58088-210">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="58088-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="58088-211">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="58088-211">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="58088-212">Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="58088-212">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="58088-213">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="58088-213">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="58088-214">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="58088-214">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="58088-215">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="58088-215">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="58088-216">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="58088-216">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span><br><br><span data-ttu-id="58088-217">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="58088-217">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="58088-218">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="58088-219">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="58088-219">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="58088-220">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="58088-221">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="58088-221">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="58088-222">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="58088-223">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-223">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="58088-224">Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-224">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="58088-225">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="58088-225">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="58088-226">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="58088-226">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="58088-227">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="58088-227">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="58088-228">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="58088-228">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="58088-229">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="58088-229">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="58088-230">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="58088-230">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="58088-231">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="58088-231">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="58088-232">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="58088-232">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="58088-233">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="58088-233">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="58088-234">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="58088-234">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="58088-235">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="58088-235">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="58088-236">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="58088-236">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="58088-237">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="58088-237">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="58088-238">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="58088-238">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="58088-239">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="58088-239">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="58088-240">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="58088-240">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="58088-241">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="58088-241">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="58088-242">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="58088-242">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="58088-243">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="58088-243">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="58088-244">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="58088-244">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="58088-245">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="58088-245">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="58088-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="58088-246"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="58088-247">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="58088-247">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="58088-248">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="58088-248">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="58088-249">Переадресация сертификатов</span><span class="sxs-lookup"><span data-stu-id="58088-249">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="58088-250">Azure</span><span class="sxs-lookup"><span data-stu-id="58088-250">Azure</span></span>

<span data-ttu-id="58088-251">Сведения о настройке Службы приложений Azure для переадресации сертификатов см. в статье [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth) (Настройка взаимной проверки подлинности TLS для Службы приложений Azure).</span><span class="sxs-lookup"><span data-stu-id="58088-251">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="58088-252">Приведенные ниже инструкции применимы к настройке приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58088-252">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="58088-253">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="58088-253">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="58088-254">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка, который использует Azure.</span><span class="sxs-lookup"><span data-stu-id="58088-254">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="58088-255">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="58088-255">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="58088-256">Другие веб-прокси</span><span class="sxs-lookup"><span data-stu-id="58088-256">Other web proxies</span></span>

<span data-ttu-id="58088-257">Если вы используете прокси-сервер не IIS или маршрутизацию запросов приложений (ARR) Службы приложений Azure, настройте прокси-сервер для переадресации сертификата, полученного в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="58088-257">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="58088-258">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="58088-258">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="58088-259">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка.</span><span class="sxs-lookup"><span data-stu-id="58088-259">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="58088-260">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="58088-260">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="58088-261">Если прокси-сервер не выполняет шифрование сертификата в кодировке base64 (как в случае с Nginx), задайте параметр `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="58088-261">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="58088-262">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="58088-262">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="58088-263">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="58088-263">Troubleshoot</span></span>

<span data-ttu-id="58088-264">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="58088-264">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="58088-265">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="58088-265">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="58088-266">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="58088-266">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="58088-267">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="58088-267">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="58088-268">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="58088-268">You can write to logs instead of the response body.</span></span> <span data-ttu-id="58088-269">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="58088-269">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="58088-270">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="58088-270">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="58088-271">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="58088-271">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="58088-272">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="58088-272">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="58088-273">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="58088-273">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="58088-274">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="58088-274">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="58088-275">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="58088-275">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="58088-276">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="58088-276">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="58088-277">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="58088-277">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="58088-278">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="58088-278">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="58088-279">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="58088-279">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="58088-280">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="58088-280">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="58088-281">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-281">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="58088-282">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="58088-282">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="58088-283">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="58088-283">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58088-284">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="58088-284">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="58088-285">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="58088-285">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58088-286">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="58088-286">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="58088-287">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="58088-287">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="58088-288">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="58088-288">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="58088-289">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="58088-289">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="58088-290">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="58088-290">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="58088-291">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="58088-291">Forwarded headers</span></span>

<span data-ttu-id="58088-292">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="58088-292">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="58088-293">Header</span><span class="sxs-lookup"><span data-stu-id="58088-293">Header</span></span> | <span data-ttu-id="58088-294">Описание</span><span class="sxs-lookup"><span data-stu-id="58088-294">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="58088-295">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="58088-295">X-Forwarded-For</span></span> | <span data-ttu-id="58088-296">Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="58088-296">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="58088-297">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="58088-297">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="58088-298">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="58088-298">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="58088-299">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="58088-299">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="58088-300">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="58088-300">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="58088-301">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="58088-301">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="58088-302">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="58088-302">X-Forwarded-Proto</span></span> | <span data-ttu-id="58088-303">Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="58088-303">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="58088-304">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="58088-304">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="58088-305">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="58088-305">X-Forwarded-Host</span></span> | <span data-ttu-id="58088-306">Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="58088-306">The original value of the Host header field.</span></span> <span data-ttu-id="58088-307">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="58088-307">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="58088-308">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="58088-308">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="58088-309">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="58088-309">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="58088-310">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="58088-310">The middleware updates:</span></span>

* <span data-ttu-id="58088-311">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) — задайте с помощью значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="58088-311">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="58088-312">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="58088-312">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="58088-313">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-313">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="58088-314">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) — задайте с помощью значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-314">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="58088-315">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) — задайте с помощью значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="58088-315">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="58088-316">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-316">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="58088-317">По умолчанию используются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="58088-317">The default settings are:</span></span>

* <span data-ttu-id="58088-318">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="58088-318">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="58088-319">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="58088-319">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="58088-320">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-320">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="58088-321">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="58088-321">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="58088-322">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="58088-322">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="58088-323">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="58088-323">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="58088-324">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="58088-324">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="58088-325">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58088-325">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="58088-326">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58088-326">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="58088-327">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="58088-327">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="58088-328">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="58088-328">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="58088-329">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-329">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="58088-330">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="58088-330">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="58088-331">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="58088-331">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="58088-332">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="58088-332">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="58088-333">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-333">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="58088-334">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="58088-334">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="58088-335">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="58088-335">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="58088-336">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-336">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="58088-337">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="58088-337">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="58088-338">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="58088-338">Nginx configuration</span></span>

<span data-ttu-id="58088-339">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="58088-339">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="58088-340">Дополнительные сведения см. в статье об [использовании заголовка Forwarded в NGINX](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="58088-340">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="58088-341">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="58088-341">Apache configuration</span></span>

<span data-ttu-id="58088-342">`X-Forwarded-For` добавляется автоматически (см. документацию по [заголовкам обратных прокси-запросов в модуле Apache mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="58088-342">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="58088-343">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="58088-343">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="58088-344">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="58088-344">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="58088-345">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="58088-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="58088-346">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="58088-346">The following example changes the default values:</span></span>

* <span data-ttu-id="58088-347">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="58088-347">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="58088-348">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="58088-348">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="58088-349">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="58088-349">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="58088-350">Параметр</span><span class="sxs-lookup"><span data-stu-id="58088-350">Option</span></span> | <span data-ttu-id="58088-351">Описание</span><span class="sxs-lookup"><span data-stu-id="58088-351">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="58088-352">Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="58088-352">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="58088-353">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="58088-353">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="58088-354">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="58088-354">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="58088-355">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="58088-355">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="58088-356">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="58088-356">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="58088-357">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="58088-357">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="58088-358">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="58088-358">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="58088-359">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="58088-359">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="58088-360">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="58088-360">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="58088-361">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="58088-361">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="58088-362">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="58088-362">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="58088-363">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="58088-363">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="58088-364">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-364">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="58088-365">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="58088-365">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="58088-366">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="58088-366">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="58088-367">Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="58088-367">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="58088-368">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="58088-368">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="58088-369">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="58088-369">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="58088-370">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-370">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="58088-371">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-371">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="58088-372">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="58088-372">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="58088-373">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="58088-373">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="58088-374">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-374">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="58088-375">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="58088-375">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="58088-376">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-376">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="58088-377">Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="58088-377">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="58088-378">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="58088-378">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="58088-379">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="58088-379">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="58088-380">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="58088-380">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="58088-381">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="58088-381">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="58088-382">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="58088-382">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="58088-383">Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="58088-383">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="58088-384">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="58088-384">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="58088-385">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="58088-385">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="58088-386">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="58088-386">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="58088-387">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="58088-387">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="58088-388">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="58088-388">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="58088-389">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="58088-389">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="58088-390">Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="58088-390">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="58088-391">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="58088-391">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="58088-392">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="58088-392">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="58088-393">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="58088-393">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="58088-394">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="58088-394">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="58088-395">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="58088-395">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="58088-396">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="58088-396">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="58088-397">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-397">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="58088-398">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="58088-398">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="58088-399">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-399">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="58088-400">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="58088-400">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="58088-401">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="58088-401">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="58088-402">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="58088-402">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="58088-403">Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="58088-403">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="58088-404">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="58088-404">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="58088-405">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="58088-405">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="58088-406">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="58088-406">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="58088-407">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="58088-407">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="58088-408">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="58088-408">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="58088-409">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="58088-409">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="58088-410">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="58088-410">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="58088-411">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="58088-411">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="58088-412">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="58088-412">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="58088-413">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="58088-413">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="58088-414">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="58088-414">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="58088-415">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="58088-415">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="58088-416">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="58088-416">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="58088-417">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="58088-417">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="58088-418">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="58088-418">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="58088-419">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="58088-419">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="58088-420">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="58088-420">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="58088-421">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="58088-421">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="58088-422">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="58088-422">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="58088-423">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="58088-423">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="58088-424">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="58088-424">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="58088-425">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="58088-425">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="58088-426">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="58088-426">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="58088-427">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="58088-427">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="58088-428">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="58088-428">Converted prefix length: 104</span></span>

<span data-ttu-id="58088-429">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="58088-429">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="58088-430">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="58088-430">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="58088-431">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="58088-431">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="58088-432">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="58088-432">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="58088-433">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="58088-433">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="58088-434">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="58088-434">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="58088-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="58088-435"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="58088-436">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="58088-436">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="58088-437">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="58088-437">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="58088-438">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="58088-438">Troubleshoot</span></span>

<span data-ttu-id="58088-439">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="58088-439">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="58088-440">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="58088-440">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="58088-441">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="58088-441">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="58088-442">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="58088-442">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="58088-443">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="58088-443">You can write to logs instead of the response body.</span></span> <span data-ttu-id="58088-444">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="58088-444">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="58088-445">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="58088-445">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="58088-446">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="58088-446">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="58088-447">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="58088-447">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="58088-448">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="58088-448">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="58088-449">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="58088-449">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="58088-450">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="58088-450">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="58088-451">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="58088-451">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="58088-452">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="58088-452">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="58088-453">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="58088-453">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="58088-454">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="58088-454">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="58088-455">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="58088-455">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="58088-456">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="58088-456">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="58088-457">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="58088-457">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="58088-458">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="58088-458">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58088-459">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="58088-459">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="58088-460">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="58088-460">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
