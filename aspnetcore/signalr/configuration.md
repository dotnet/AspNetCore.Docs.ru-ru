---
title: Конфигурация SignalR ASP.NET Core
author: bradygaster
description: Узнайте, как настроить ASP.NET Core SignalR приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: cef05b32731e0930d7a3cfc6fe4502236b07a236
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972071"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="44eff-103">Конфигурация SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44eff-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="44eff-104">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="44eff-105">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="44eff-106">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="44eff-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="44eff-107">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="44eff-108">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44eff-109">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="44eff-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="44eff-110">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="44eff-111">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="44eff-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="44eff-112">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44eff-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="44eff-113">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="44eff-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="44eff-114">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="44eff-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="44eff-115">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="44eff-116">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="44eff-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="44eff-117">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="44eff-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="44eff-118">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-118">MessagePack serialization options</span></span>

<span data-ttu-id="44eff-119">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="44eff-120">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-121">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="44eff-122">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="44eff-122">Configure server options</span></span>

<span data-ttu-id="44eff-123">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="44eff-124">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-124">Option</span></span> | <span data-ttu-id="44eff-125">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-125">Default Value</span></span> | <span data-ttu-id="44eff-126">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="44eff-127">30 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-127">30 seconds</span></span> | <span data-ttu-id="44eff-128">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="44eff-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="44eff-129">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="44eff-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="44eff-130">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="44eff-131">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-131">15 seconds</span></span> | <span data-ttu-id="44eff-132">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="44eff-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="44eff-133">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-134">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-135">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-135">15 seconds</span></span> | <span data-ttu-id="44eff-136">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="44eff-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="44eff-137">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="44eff-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="44eff-138">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="44eff-139">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="44eff-139">All installed protocols</span></span> | <span data-ttu-id="44eff-140">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="44eff-140">Protocols supported by this hub.</span></span> <span data-ttu-id="44eff-141">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="44eff-142">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="44eff-143">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="44eff-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="44eff-144">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="44eff-145">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="44eff-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="44eff-146">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-146">32 KB</span></span> | <span data-ttu-id="44eff-147">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="44eff-148">1</span><span class="sxs-lookup"><span data-stu-id="44eff-148">1</span></span> | <span data-ttu-id="44eff-149">Максимальное число методов концентратора, которые каждый клиент может вызывать параллельно перед постановкой в очередь.</span><span class="sxs-lookup"><span data-stu-id="44eff-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="44eff-150">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="44eff-151">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="44eff-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="44eff-152">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="44eff-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="44eff-153">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="44eff-154">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="44eff-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="44eff-155">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44eff-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="44eff-156">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-156">Option</span></span> | <span data-ttu-id="44eff-157">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-157">Default Value</span></span> | <span data-ttu-id="44eff-158">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="44eff-159">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-159">32 KB</span></span> | <span data-ttu-id="44eff-160">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="44eff-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="44eff-161">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="44eff-162">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="44eff-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="44eff-163">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="44eff-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="44eff-164">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-164">32 KB</span></span> | <span data-ttu-id="44eff-165">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="44eff-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="44eff-166">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="44eff-167">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-167">All Transports are enabled.</span></span> | <span data-ttu-id="44eff-168">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="44eff-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="44eff-169">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-169">See below.</span></span> | <span data-ttu-id="44eff-170">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="44eff-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="44eff-171">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-171">See below.</span></span> | <span data-ttu-id="44eff-172">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="44eff-173">0</span><span class="sxs-lookup"><span data-stu-id="44eff-173">0</span></span> | <span data-ttu-id="44eff-174">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="44eff-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="44eff-175">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="44eff-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="44eff-176">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="44eff-177">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-177">Option</span></span> | <span data-ttu-id="44eff-178">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-178">Default Value</span></span> | <span data-ttu-id="44eff-179">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="44eff-180">90 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-180">90 seconds</span></span> | <span data-ttu-id="44eff-181">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="44eff-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="44eff-182">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="44eff-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="44eff-183">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="44eff-184">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-184">Option</span></span> | <span data-ttu-id="44eff-185">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-185">Default Value</span></span> | <span data-ttu-id="44eff-186">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="44eff-187">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-187">5 seconds</span></span> | <span data-ttu-id="44eff-188">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="44eff-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="44eff-189">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="44eff-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="44eff-190">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="44eff-191">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="44eff-191">Configure client options</span></span>

<span data-ttu-id="44eff-192">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="44eff-193">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="44eff-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="44eff-194">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="44eff-194">Configure logging</span></span>

<span data-ttu-id="44eff-195">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="44eff-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="44eff-196">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="44eff-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="44eff-197">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="44eff-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-198">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="44eff-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="44eff-199">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="44eff-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="44eff-200">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="44eff-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="44eff-201">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="44eff-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="44eff-202">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="44eff-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="44eff-203">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="44eff-204">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="44eff-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="44eff-205">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="44eff-206">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="44eff-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="44eff-207">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-207">The following table lists the available log levels.</span></span> <span data-ttu-id="44eff-208">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="44eff-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="44eff-209">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="44eff-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="44eff-210">Строка</span><span class="sxs-lookup"><span data-stu-id="44eff-210">String</span></span>                      | <span data-ttu-id="44eff-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="44eff-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="44eff-212">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="44eff-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="44eff-213">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="44eff-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="44eff-214">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="44eff-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="44eff-215">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="44eff-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="44eff-216">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="44eff-217">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="44eff-218">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="44eff-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="44eff-219">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="44eff-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="44eff-220">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="44eff-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="44eff-221">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="44eff-221">Configure allowed transports</span></span>

<span data-ttu-id="44eff-222">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="44eff-223">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="44eff-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="44eff-224">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-224">All transports are enabled by default.</span></span>

<span data-ttu-id="44eff-225">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="44eff-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="44eff-226">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="44eff-227">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="44eff-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="44eff-228">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="44eff-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="44eff-229">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="44eff-230">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="44eff-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="44eff-231">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="44eff-231">Configure bearer authentication</span></span>

<span data-ttu-id="44eff-232">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="44eff-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="44eff-233">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="44eff-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="44eff-234">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="44eff-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="44eff-235">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="44eff-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="44eff-236">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="44eff-237">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="44eff-238">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="44eff-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="44eff-239">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="44eff-240">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="44eff-241">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="44eff-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="44eff-242">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="44eff-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-243">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-244">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-244">Option</span></span> | <span data-ttu-id="44eff-245">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-245">Default value</span></span> | <span data-ttu-id="44eff-246">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="44eff-247">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-248">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-248">Timeout for server activity.</span></span> <span data-ttu-id="44eff-249">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-250">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-251">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="44eff-252">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-252">15 seconds</span></span> | <span data-ttu-id="44eff-253">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-254">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-255">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-256">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-257">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-257">15 seconds</span></span> | <span data-ttu-id="44eff-258">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-259">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-260">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="44eff-261">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="44eff-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-263">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-263">Option</span></span> | <span data-ttu-id="44eff-264">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-264">Default value</span></span> | <span data-ttu-id="44eff-265">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="44eff-266">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-267">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-267">Timeout for server activity.</span></span> <span data-ttu-id="44eff-268">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="44eff-269">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-270">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="44eff-271">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-272">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-273">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-274">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-275">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-275">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-276">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-276">Option</span></span> | <span data-ttu-id="44eff-277">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-277">Default value</span></span> | <span data-ttu-id="44eff-278">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="44eff-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="44eff-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="44eff-280">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-281">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-281">Timeout for server activity.</span></span> <span data-ttu-id="44eff-282">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-283">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-284">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="44eff-285">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-285">15 seconds</span></span> | <span data-ttu-id="44eff-286">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-287">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-288">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-289">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="44eff-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="44eff-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="44eff-291">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-292">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-293">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-294">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="44eff-295">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="44eff-295">Configure additional options</span></span>

<span data-ttu-id="44eff-296">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="44eff-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-297">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-298">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="44eff-298">.NET Option</span></span> |  <span data-ttu-id="44eff-299">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-299">Default value</span></span> | <span data-ttu-id="44eff-300">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="44eff-301">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="44eff-302">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-303">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-304">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="44eff-305">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-305">Empty</span></span> | <span data-ttu-id="44eff-306">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44eff-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="44eff-307">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-307">Empty</span></span> | <span data-ttu-id="44eff-308">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="44eff-309">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-309">Empty</span></span> | <span data-ttu-id="44eff-310">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="44eff-311">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-311">5 seconds</span></span> | <span data-ttu-id="44eff-312">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-312">WebSockets only.</span></span> <span data-ttu-id="44eff-313">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="44eff-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="44eff-314">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="44eff-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="44eff-315">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-315">Empty</span></span> | <span data-ttu-id="44eff-316">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="44eff-317">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="44eff-318">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="44eff-319">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="44eff-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="44eff-320">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="44eff-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="44eff-321">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="44eff-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="44eff-322">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="44eff-323">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="44eff-324">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="44eff-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="44eff-325">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="44eff-326">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="44eff-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="44eff-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-328">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-328">JavaScript Option</span></span> | <span data-ttu-id="44eff-329">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-329">Default Value</span></span> | <span data-ttu-id="44eff-330">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="44eff-331">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="44eff-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="44eff-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="44eff-333">Словарь заголовков, отправленных с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="44eff-334">Отправка заголовков в браузере не работает для WebSockets или <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> потока.</span><span class="sxs-lookup"><span data-stu-id="44eff-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="44eff-335">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="44eff-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="44eff-336">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-337">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-338">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="44eff-339">Указывает, будут ли учетные данные отправляться с запросом CORS.</span><span class="sxs-lookup"><span data-stu-id="44eff-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="44eff-340">Служба приложений Azure использует cookie для прикрепленных сеансов s и требует, чтобы этот параметр был включен правильно.</span><span class="sxs-lookup"><span data-stu-id="44eff-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="44eff-341">Дополнительные сведения о CORS с SignalR см. в разделе <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="44eff-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-342">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-342">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-343">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="44eff-343">Java Option</span></span> | <span data-ttu-id="44eff-344">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-344">Default Value</span></span> | <span data-ttu-id="44eff-345">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="44eff-346">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="44eff-347">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-348">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-349">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="44eff-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="44eff-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="44eff-351">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-351">Empty</span></span> | <span data-ttu-id="44eff-352">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="44eff-353">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="44eff-354">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="44eff-355">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="44eff-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="44eff-356">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44eff-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="44eff-357">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="44eff-358">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="44eff-359">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="44eff-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="44eff-360">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="44eff-361">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44eff-362">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="44eff-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="44eff-363">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="44eff-364">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="44eff-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="44eff-365">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44eff-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="44eff-366">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="44eff-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="44eff-367">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="44eff-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="44eff-368">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="44eff-369">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="44eff-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="44eff-370">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="44eff-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="44eff-371">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-371">MessagePack serialization options</span></span>

<span data-ttu-id="44eff-372">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="44eff-373">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-374">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="44eff-375">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="44eff-375">Configure server options</span></span>

<span data-ttu-id="44eff-376">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="44eff-377">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-377">Option</span></span> | <span data-ttu-id="44eff-378">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-378">Default Value</span></span> | <span data-ttu-id="44eff-379">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="44eff-380">30 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-380">30 seconds</span></span> | <span data-ttu-id="44eff-381">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="44eff-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="44eff-382">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="44eff-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="44eff-383">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="44eff-384">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-384">15 seconds</span></span> | <span data-ttu-id="44eff-385">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="44eff-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="44eff-386">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-387">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-388">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-388">15 seconds</span></span> | <span data-ttu-id="44eff-389">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="44eff-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="44eff-390">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="44eff-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="44eff-391">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="44eff-392">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="44eff-392">All installed protocols</span></span> | <span data-ttu-id="44eff-393">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="44eff-393">Protocols supported by this hub.</span></span> <span data-ttu-id="44eff-394">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="44eff-395">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="44eff-396">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="44eff-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="44eff-397">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="44eff-398">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="44eff-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="44eff-399">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-399">32 KB</span></span> | <span data-ttu-id="44eff-400">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="44eff-401">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="44eff-402">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="44eff-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="44eff-403">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="44eff-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="44eff-404">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="44eff-405">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="44eff-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="44eff-406">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44eff-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="44eff-407">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-407">Option</span></span> | <span data-ttu-id="44eff-408">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-408">Default Value</span></span> | <span data-ttu-id="44eff-409">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="44eff-410">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-410">32 KB</span></span> | <span data-ttu-id="44eff-411">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="44eff-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="44eff-412">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="44eff-413">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="44eff-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="44eff-414">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="44eff-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="44eff-415">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-415">32 KB</span></span> | <span data-ttu-id="44eff-416">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="44eff-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="44eff-417">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="44eff-418">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-418">All Transports are enabled.</span></span> | <span data-ttu-id="44eff-419">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="44eff-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="44eff-420">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-420">See below.</span></span> | <span data-ttu-id="44eff-421">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="44eff-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="44eff-422">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-422">See below.</span></span> | <span data-ttu-id="44eff-423">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="44eff-424">0</span><span class="sxs-lookup"><span data-stu-id="44eff-424">0</span></span> | <span data-ttu-id="44eff-425">Укажите минимальную версию протокола Negotiate.</span><span class="sxs-lookup"><span data-stu-id="44eff-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="44eff-426">Используется для ограничения клиентов на более новые версии.</span><span class="sxs-lookup"><span data-stu-id="44eff-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="44eff-427">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="44eff-428">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-428">Option</span></span> | <span data-ttu-id="44eff-429">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-429">Default Value</span></span> | <span data-ttu-id="44eff-430">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="44eff-431">90 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-431">90 seconds</span></span> | <span data-ttu-id="44eff-432">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="44eff-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="44eff-433">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="44eff-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="44eff-434">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="44eff-435">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-435">Option</span></span> | <span data-ttu-id="44eff-436">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-436">Default Value</span></span> | <span data-ttu-id="44eff-437">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="44eff-438">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-438">5 seconds</span></span> | <span data-ttu-id="44eff-439">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="44eff-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="44eff-440">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="44eff-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="44eff-441">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="44eff-442">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="44eff-442">Configure client options</span></span>

<span data-ttu-id="44eff-443">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="44eff-444">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="44eff-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="44eff-445">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="44eff-445">Configure logging</span></span>

<span data-ttu-id="44eff-446">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="44eff-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="44eff-447">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="44eff-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="44eff-448">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="44eff-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-449">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="44eff-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="44eff-450">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="44eff-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="44eff-451">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="44eff-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="44eff-452">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="44eff-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="44eff-453">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="44eff-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="44eff-454">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="44eff-455">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="44eff-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="44eff-456">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="44eff-457">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="44eff-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="44eff-458">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-458">The following table lists the available log levels.</span></span> <span data-ttu-id="44eff-459">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="44eff-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="44eff-460">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="44eff-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="44eff-461">Строка</span><span class="sxs-lookup"><span data-stu-id="44eff-461">String</span></span>                      | <span data-ttu-id="44eff-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="44eff-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="44eff-463">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="44eff-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="44eff-464">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="44eff-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="44eff-465">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="44eff-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="44eff-466">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="44eff-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="44eff-467">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="44eff-468">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="44eff-469">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="44eff-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="44eff-470">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="44eff-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="44eff-471">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="44eff-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="44eff-472">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="44eff-472">Configure allowed transports</span></span>

<span data-ttu-id="44eff-473">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="44eff-474">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="44eff-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="44eff-475">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-475">All transports are enabled by default.</span></span>

<span data-ttu-id="44eff-476">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="44eff-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="44eff-477">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="44eff-478">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="44eff-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="44eff-479">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="44eff-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="44eff-480">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="44eff-481">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="44eff-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="44eff-482">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="44eff-482">Configure bearer authentication</span></span>

<span data-ttu-id="44eff-483">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="44eff-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="44eff-484">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="44eff-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="44eff-485">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="44eff-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="44eff-486">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="44eff-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="44eff-487">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="44eff-488">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="44eff-489">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="44eff-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="44eff-490">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="44eff-491">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="44eff-492">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="44eff-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="44eff-493">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="44eff-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-494">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-495">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-495">Option</span></span> | <span data-ttu-id="44eff-496">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-496">Default value</span></span> | <span data-ttu-id="44eff-497">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="44eff-498">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-499">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-499">Timeout for server activity.</span></span> <span data-ttu-id="44eff-500">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-501">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-502">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="44eff-503">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-503">15 seconds</span></span> | <span data-ttu-id="44eff-504">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-505">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-506">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-507">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-508">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-508">15 seconds</span></span> | <span data-ttu-id="44eff-509">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-510">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-511">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="44eff-512">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="44eff-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-514">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-514">Option</span></span> | <span data-ttu-id="44eff-515">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-515">Default value</span></span> | <span data-ttu-id="44eff-516">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="44eff-517">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-518">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-518">Timeout for server activity.</span></span> <span data-ttu-id="44eff-519">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="44eff-520">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-521">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="44eff-522">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-523">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-524">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-525">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-526">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-526">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-527">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-527">Option</span></span> | <span data-ttu-id="44eff-528">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-528">Default value</span></span> | <span data-ttu-id="44eff-529">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="44eff-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="44eff-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="44eff-531">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-532">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-532">Timeout for server activity.</span></span> <span data-ttu-id="44eff-533">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-534">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-535">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="44eff-536">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-536">15 seconds</span></span> | <span data-ttu-id="44eff-537">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-538">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-539">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-540">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="44eff-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="44eff-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="44eff-542">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-543">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-544">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-545">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="44eff-546">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="44eff-546">Configure additional options</span></span>

<span data-ttu-id="44eff-547">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="44eff-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-548">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-549">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="44eff-549">.NET Option</span></span> |  <span data-ttu-id="44eff-550">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-550">Default value</span></span> | <span data-ttu-id="44eff-551">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="44eff-552">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="44eff-553">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-554">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-555">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="44eff-556">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-556">Empty</span></span> | <span data-ttu-id="44eff-557">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44eff-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="44eff-558">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-558">Empty</span></span> | <span data-ttu-id="44eff-559">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="44eff-560">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-560">Empty</span></span> | <span data-ttu-id="44eff-561">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="44eff-562">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-562">5 seconds</span></span> | <span data-ttu-id="44eff-563">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-563">WebSockets only.</span></span> <span data-ttu-id="44eff-564">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="44eff-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="44eff-565">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="44eff-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="44eff-566">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-566">Empty</span></span> | <span data-ttu-id="44eff-567">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="44eff-568">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="44eff-569">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="44eff-570">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="44eff-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="44eff-571">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="44eff-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="44eff-572">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="44eff-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="44eff-573">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="44eff-574">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="44eff-575">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="44eff-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="44eff-576">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="44eff-577">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="44eff-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="44eff-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-579">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-579">JavaScript Option</span></span> | <span data-ttu-id="44eff-580">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-580">Default Value</span></span> | <span data-ttu-id="44eff-581">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="44eff-582">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="44eff-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="44eff-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="44eff-584">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="44eff-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="44eff-585">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-586">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-587">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-588">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-588">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-589">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="44eff-589">Java Option</span></span> | <span data-ttu-id="44eff-590">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-590">Default Value</span></span> | <span data-ttu-id="44eff-591">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="44eff-592">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="44eff-593">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-594">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-595">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="44eff-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="44eff-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="44eff-597">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-597">Empty</span></span> | <span data-ttu-id="44eff-598">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="44eff-599">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="44eff-600">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="44eff-601">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="44eff-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="44eff-602">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44eff-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="44eff-603">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="44eff-604">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="44eff-605">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="44eff-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="44eff-606">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="44eff-607">`AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="44eff-608">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="44eff-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="44eff-609">Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="44eff-610">Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="44eff-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="44eff-611">Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44eff-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="44eff-612">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="44eff-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="44eff-613">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="44eff-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="44eff-614">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="44eff-615">Переключиться на Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="44eff-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="44eff-616">Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="44eff-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="44eff-617">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-617">MessagePack serialization options</span></span>

<span data-ttu-id="44eff-618">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="44eff-619">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-620">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="44eff-621">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="44eff-621">Configure server options</span></span>

<span data-ttu-id="44eff-622">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="44eff-623">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-623">Option</span></span> | <span data-ttu-id="44eff-624">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-624">Default Value</span></span> | <span data-ttu-id="44eff-625">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="44eff-626">30 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-626">30 seconds</span></span> | <span data-ttu-id="44eff-627">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="44eff-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="44eff-628">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="44eff-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="44eff-629">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="44eff-630">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-630">15 seconds</span></span> | <span data-ttu-id="44eff-631">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="44eff-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="44eff-632">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-633">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-634">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-634">15 seconds</span></span> | <span data-ttu-id="44eff-635">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="44eff-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="44eff-636">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="44eff-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="44eff-637">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="44eff-638">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="44eff-638">All installed protocols</span></span> | <span data-ttu-id="44eff-639">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="44eff-639">Protocols supported by this hub.</span></span> <span data-ttu-id="44eff-640">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="44eff-641">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="44eff-642">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="44eff-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="44eff-643">Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="44eff-644">При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.</span><span class="sxs-lookup"><span data-stu-id="44eff-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="44eff-645">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-645">32 KB</span></span> | <span data-ttu-id="44eff-646">Максимальный размер одного входящего сообщения концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="44eff-647">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="44eff-648">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="44eff-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="44eff-649">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="44eff-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="44eff-650">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="44eff-651">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="44eff-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="44eff-652">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44eff-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="44eff-653">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-653">Option</span></span> | <span data-ttu-id="44eff-654">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-654">Default Value</span></span> | <span data-ttu-id="44eff-655">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="44eff-656">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-656">32 KB</span></span> | <span data-ttu-id="44eff-657">Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="44eff-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="44eff-658">Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="44eff-659">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="44eff-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="44eff-660">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="44eff-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="44eff-661">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-661">32 KB</span></span> | <span data-ttu-id="44eff-662">Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой.</span><span class="sxs-lookup"><span data-stu-id="44eff-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="44eff-663">Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="44eff-664">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-664">All Transports are enabled.</span></span> | <span data-ttu-id="44eff-665">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="44eff-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="44eff-666">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-666">See below.</span></span> | <span data-ttu-id="44eff-667">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="44eff-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="44eff-668">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-668">See below.</span></span> | <span data-ttu-id="44eff-669">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="44eff-670">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="44eff-671">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-671">Option</span></span> | <span data-ttu-id="44eff-672">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-672">Default Value</span></span> | <span data-ttu-id="44eff-673">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="44eff-674">90 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-674">90 seconds</span></span> | <span data-ttu-id="44eff-675">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="44eff-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="44eff-676">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="44eff-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="44eff-677">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="44eff-678">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-678">Option</span></span> | <span data-ttu-id="44eff-679">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-679">Default Value</span></span> | <span data-ttu-id="44eff-680">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="44eff-681">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-681">5 seconds</span></span> | <span data-ttu-id="44eff-682">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="44eff-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="44eff-683">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="44eff-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="44eff-684">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="44eff-685">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="44eff-685">Configure client options</span></span>

<span data-ttu-id="44eff-686">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="44eff-687">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="44eff-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="44eff-688">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="44eff-688">Configure logging</span></span>

<span data-ttu-id="44eff-689">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="44eff-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="44eff-690">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="44eff-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="44eff-691">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="44eff-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-692">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="44eff-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="44eff-693">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="44eff-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="44eff-694">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="44eff-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="44eff-695">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="44eff-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="44eff-696">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="44eff-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="44eff-697">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="44eff-698">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="44eff-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="44eff-699">Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="44eff-700">Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.</span><span class="sxs-lookup"><span data-stu-id="44eff-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="44eff-701">В следующей таблице перечислены доступные уровни ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-701">The following table lists the available log levels.</span></span> <span data-ttu-id="44eff-702">Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал.</span><span class="sxs-lookup"><span data-stu-id="44eff-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="44eff-703">Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.</span><span class="sxs-lookup"><span data-stu-id="44eff-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="44eff-704">Строка</span><span class="sxs-lookup"><span data-stu-id="44eff-704">String</span></span>                      | <span data-ttu-id="44eff-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="44eff-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="44eff-706">`info`**или**`information`</span><span class="sxs-lookup"><span data-stu-id="44eff-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="44eff-707">`warn`**или**`warning`</span><span class="sxs-lookup"><span data-stu-id="44eff-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="44eff-708">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="44eff-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="44eff-709">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="44eff-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="44eff-710">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="44eff-711">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="44eff-712">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="44eff-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="44eff-713">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="44eff-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="44eff-714">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="44eff-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="44eff-715">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="44eff-715">Configure allowed transports</span></span>

<span data-ttu-id="44eff-716">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="44eff-717">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="44eff-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="44eff-718">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-718">All transports are enabled by default.</span></span>

<span data-ttu-id="44eff-719">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="44eff-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="44eff-720">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="44eff-721">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="44eff-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="44eff-722">В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="44eff-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="44eff-723">Клиент Java по умолчанию использует транспорт WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="44eff-724">SignalRКлиент Java пока не поддерживает откат транспорта.</span><span class="sxs-lookup"><span data-stu-id="44eff-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="44eff-725">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="44eff-725">Configure bearer authentication</span></span>

<span data-ttu-id="44eff-726">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="44eff-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="44eff-727">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="44eff-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="44eff-728">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="44eff-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="44eff-729">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="44eff-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="44eff-730">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="44eff-731">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="44eff-732">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="44eff-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="44eff-733">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="44eff-734">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="44eff-735">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="44eff-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="44eff-736">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="44eff-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-737">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-738">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-738">Option</span></span> | <span data-ttu-id="44eff-739">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-739">Default value</span></span> | <span data-ttu-id="44eff-740">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="44eff-741">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-742">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-742">Timeout for server activity.</span></span> <span data-ttu-id="44eff-743">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-744">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-745">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="44eff-746">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-746">15 seconds</span></span> | <span data-ttu-id="44eff-747">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-748">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-749">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-750">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-751">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-751">15 seconds</span></span> | <span data-ttu-id="44eff-752">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-753">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-754">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="44eff-755">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="44eff-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-757">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-757">Option</span></span> | <span data-ttu-id="44eff-758">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-758">Default value</span></span> | <span data-ttu-id="44eff-759">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="44eff-760">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-761">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-761">Timeout for server activity.</span></span> <span data-ttu-id="44eff-762">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="44eff-763">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-764">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="44eff-765">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-766">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-767">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-768">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-769">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-769">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-770">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-770">Option</span></span> | <span data-ttu-id="44eff-771">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-771">Default value</span></span> | <span data-ttu-id="44eff-772">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="44eff-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="44eff-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="44eff-774">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-775">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-775">Timeout for server activity.</span></span> <span data-ttu-id="44eff-776">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-777">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-778">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="44eff-779">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-779">15 seconds</span></span> | <span data-ttu-id="44eff-780">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-781">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-782">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-783">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="44eff-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="44eff-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="44eff-785">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-786">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-787">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-788">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="44eff-789">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="44eff-789">Configure additional options</span></span>

<span data-ttu-id="44eff-790">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="44eff-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-791">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-792">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="44eff-792">.NET Option</span></span> |  <span data-ttu-id="44eff-793">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-793">Default value</span></span> | <span data-ttu-id="44eff-794">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="44eff-795">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="44eff-796">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-797">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-798">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="44eff-799">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-799">Empty</span></span> | <span data-ttu-id="44eff-800">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44eff-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="44eff-801">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-801">Empty</span></span> | <span data-ttu-id="44eff-802">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="44eff-803">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-803">Empty</span></span> | <span data-ttu-id="44eff-804">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="44eff-805">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-805">5 seconds</span></span> | <span data-ttu-id="44eff-806">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-806">WebSockets only.</span></span> <span data-ttu-id="44eff-807">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="44eff-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="44eff-808">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="44eff-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="44eff-809">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-809">Empty</span></span> | <span data-ttu-id="44eff-810">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="44eff-811">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="44eff-812">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="44eff-813">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="44eff-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="44eff-814">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="44eff-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="44eff-815">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="44eff-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="44eff-816">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="44eff-817">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="44eff-818">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="44eff-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="44eff-819">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="44eff-820">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="44eff-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="44eff-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-822">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-822">JavaScript Option</span></span> | <span data-ttu-id="44eff-823">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-823">Default Value</span></span> | <span data-ttu-id="44eff-824">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="44eff-825">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="44eff-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="44eff-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="44eff-827">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="44eff-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="44eff-828">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-829">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-830">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-831">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-831">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-832">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="44eff-832">Java Option</span></span> | <span data-ttu-id="44eff-833">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-833">Default Value</span></span> | <span data-ttu-id="44eff-834">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="44eff-835">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="44eff-836">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-837">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-838">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="44eff-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="44eff-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="44eff-840">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-840">Empty</span></span> | <span data-ttu-id="44eff-841">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="44eff-842">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="44eff-843">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="44eff-844">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="44eff-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="44eff-845">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44eff-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="44eff-846">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="44eff-847">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="44eff-848">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="44eff-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="44eff-849">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="44eff-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="44eff-850">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="44eff-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="44eff-851">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="44eff-852">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="44eff-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="44eff-853">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44eff-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="44eff-854">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="44eff-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="44eff-855">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="44eff-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="44eff-856">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="44eff-857">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-857">MessagePack serialization options</span></span>

<span data-ttu-id="44eff-858">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="44eff-859">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-860">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="44eff-861">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="44eff-861">Configure server options</span></span>

<span data-ttu-id="44eff-862">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="44eff-863">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-863">Option</span></span> | <span data-ttu-id="44eff-864">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-864">Default Value</span></span> | <span data-ttu-id="44eff-865">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="44eff-866">30 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-866">30 seconds</span></span> | <span data-ttu-id="44eff-867">Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности").</span><span class="sxs-lookup"><span data-stu-id="44eff-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="44eff-868">Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано.</span><span class="sxs-lookup"><span data-stu-id="44eff-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="44eff-869">Рекомендуемое значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="44eff-870">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-870">15 seconds</span></span> | <span data-ttu-id="44eff-871">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="44eff-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="44eff-872">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-873">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-874">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-874">15 seconds</span></span> | <span data-ttu-id="44eff-875">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="44eff-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="44eff-876">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="44eff-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="44eff-877">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="44eff-878">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="44eff-878">All installed protocols</span></span> | <span data-ttu-id="44eff-879">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="44eff-879">Protocols supported by this hub.</span></span> <span data-ttu-id="44eff-880">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="44eff-881">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="44eff-882">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="44eff-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="44eff-883">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="44eff-884">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="44eff-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="44eff-885">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="44eff-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="44eff-886">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="44eff-887">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="44eff-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="44eff-888">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44eff-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="44eff-889">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-889">Option</span></span> | <span data-ttu-id="44eff-890">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-890">Default Value</span></span> | <span data-ttu-id="44eff-891">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="44eff-892">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-892">32 KB</span></span> | <span data-ttu-id="44eff-893">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="44eff-894">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="44eff-895">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="44eff-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="44eff-896">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="44eff-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="44eff-897">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-897">32 KB</span></span> | <span data-ttu-id="44eff-898">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="44eff-899">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="44eff-900">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-900">All Transports are enabled.</span></span> | <span data-ttu-id="44eff-901">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="44eff-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="44eff-902">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-902">See below.</span></span> | <span data-ttu-id="44eff-903">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="44eff-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="44eff-904">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-904">See below.</span></span> | <span data-ttu-id="44eff-905">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="44eff-906">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="44eff-907">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-907">Option</span></span> | <span data-ttu-id="44eff-908">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-908">Default Value</span></span> | <span data-ttu-id="44eff-909">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="44eff-910">90 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-910">90 seconds</span></span> | <span data-ttu-id="44eff-911">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="44eff-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="44eff-912">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="44eff-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="44eff-913">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="44eff-914">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-914">Option</span></span> | <span data-ttu-id="44eff-915">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-915">Default Value</span></span> | <span data-ttu-id="44eff-916">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="44eff-917">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-917">5 seconds</span></span> | <span data-ttu-id="44eff-918">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="44eff-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="44eff-919">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="44eff-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="44eff-920">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="44eff-921">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="44eff-921">Configure client options</span></span>

<span data-ttu-id="44eff-922">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="44eff-923">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="44eff-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="44eff-924">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="44eff-924">Configure logging</span></span>

<span data-ttu-id="44eff-925">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="44eff-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="44eff-926">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="44eff-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="44eff-927">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="44eff-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-928">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="44eff-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="44eff-929">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="44eff-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="44eff-930">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="44eff-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="44eff-931">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="44eff-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="44eff-932">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="44eff-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="44eff-933">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="44eff-934">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="44eff-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="44eff-935">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="44eff-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="44eff-936">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="44eff-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="44eff-937">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="44eff-938">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="44eff-939">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="44eff-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="44eff-940">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="44eff-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="44eff-941">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="44eff-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="44eff-942">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="44eff-942">Configure allowed transports</span></span>

<span data-ttu-id="44eff-943">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="44eff-944">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="44eff-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="44eff-945">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-945">All transports are enabled by default.</span></span>

<span data-ttu-id="44eff-946">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="44eff-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="44eff-947">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="44eff-948">В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.</span><span class="sxs-lookup"><span data-stu-id="44eff-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="44eff-949">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="44eff-949">Configure bearer authentication</span></span>

<span data-ttu-id="44eff-950">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="44eff-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="44eff-951">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="44eff-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="44eff-952">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="44eff-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="44eff-953">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="44eff-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="44eff-954">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="44eff-955">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="44eff-956">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="44eff-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="44eff-957">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="44eff-958">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="44eff-959">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="44eff-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="44eff-960">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="44eff-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-961">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-962">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-962">Option</span></span> | <span data-ttu-id="44eff-963">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-963">Default value</span></span> | <span data-ttu-id="44eff-964">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="44eff-965">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-966">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-966">Timeout for server activity.</span></span> <span data-ttu-id="44eff-967">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-968">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-969">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="44eff-970">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-970">15 seconds</span></span> | <span data-ttu-id="44eff-971">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-972">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-973">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-974">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-975">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-975">15 seconds</span></span> | <span data-ttu-id="44eff-976">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-977">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-978">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="44eff-979">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="44eff-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-981">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-981">Option</span></span> | <span data-ttu-id="44eff-982">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-982">Default value</span></span> | <span data-ttu-id="44eff-983">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="44eff-984">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-985">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-985">Timeout for server activity.</span></span> <span data-ttu-id="44eff-986">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="44eff-987">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-988">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="44eff-989">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-990">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-991">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-992">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-993">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-993">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-994">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-994">Option</span></span> | <span data-ttu-id="44eff-995">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-995">Default value</span></span> | <span data-ttu-id="44eff-996">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="44eff-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="44eff-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="44eff-998">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-999">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-999">Timeout for server activity.</span></span> <span data-ttu-id="44eff-1000">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-1001">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-1002">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="44eff-1003">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-1003">15 seconds</span></span> | <span data-ttu-id="44eff-1004">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-1005">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-1006">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-1007">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="44eff-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="44eff-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="44eff-1009">15 секунд (15 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="44eff-1010">Определяет интервал, с которым клиент отправляет сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="44eff-1011">Отправка любого сообщения от клиента сбрасывает таймер до начала интервала.</span><span class="sxs-lookup"><span data-stu-id="44eff-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="44eff-1012">Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="44eff-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="44eff-1013">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="44eff-1013">Configure additional options</span></span>

<span data-ttu-id="44eff-1014">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="44eff-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-1016">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="44eff-1016">.NET Option</span></span> |  <span data-ttu-id="44eff-1017">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1017">Default value</span></span> | <span data-ttu-id="44eff-1018">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="44eff-1019">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="44eff-1020">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-1021">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-1022">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="44eff-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1023">Empty</span></span> | <span data-ttu-id="44eff-1024">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44eff-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="44eff-1025">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1025">Empty</span></span> | <span data-ttu-id="44eff-1026">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="44eff-1027">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1027">Empty</span></span> | <span data-ttu-id="44eff-1028">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="44eff-1029">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-1029">5 seconds</span></span> | <span data-ttu-id="44eff-1030">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-1030">WebSockets only.</span></span> <span data-ttu-id="44eff-1031">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="44eff-1032">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="44eff-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="44eff-1033">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1033">Empty</span></span> | <span data-ttu-id="44eff-1034">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="44eff-1035">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="44eff-1036">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="44eff-1037">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="44eff-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="44eff-1038">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="44eff-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="44eff-1039">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="44eff-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="44eff-1040">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="44eff-1041">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="44eff-1042">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="44eff-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="44eff-1043">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="44eff-1044">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="44eff-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="44eff-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-1046">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-1046">JavaScript Option</span></span> | <span data-ttu-id="44eff-1047">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1047">Default Value</span></span> | <span data-ttu-id="44eff-1048">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="44eff-1049">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="44eff-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="44eff-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="44eff-1051">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="44eff-1052">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-1053">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-1054">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-1055">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-1056">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="44eff-1056">Java Option</span></span> | <span data-ttu-id="44eff-1057">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1057">Default Value</span></span> | <span data-ttu-id="44eff-1058">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="44eff-1059">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="44eff-1060">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-1061">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-1062">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="44eff-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="44eff-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="44eff-1064">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1064">Empty</span></span> | <span data-ttu-id="44eff-1065">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="44eff-1066">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="44eff-1067">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="44eff-1068">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="44eff-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="44eff-1069">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44eff-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="44eff-1070">Параметры сериализации JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="44eff-1071">ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="44eff-1072">Для каждого протокола предусмотрены параметры конфигурации сериализации.</span><span class="sxs-lookup"><span data-stu-id="44eff-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="44eff-1073">Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод.</span><span class="sxs-lookup"><span data-stu-id="44eff-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="44eff-1074">`AddJsonProtocol`Метод принимает делегат, который получает `options` объект.</span><span class="sxs-lookup"><span data-stu-id="44eff-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="44eff-1075">Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="44eff-1076">Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="44eff-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="44eff-1077">Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="44eff-1078">В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="44eff-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="44eff-1079">`Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:</span><span class="sxs-lookup"><span data-stu-id="44eff-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="44eff-1080">В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="44eff-1081">Параметры сериализации MessagePack</span><span class="sxs-lookup"><span data-stu-id="44eff-1081">MessagePack serialization options</span></span>

<span data-ttu-id="44eff-1082">MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="44eff-1083">Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="44eff-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-1084">В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.</span><span class="sxs-lookup"><span data-stu-id="44eff-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="44eff-1085">Настройка параметров сервера</span><span class="sxs-lookup"><span data-stu-id="44eff-1085">Configure server options</span></span>

<span data-ttu-id="44eff-1086">В следующей таблице описаны параметры для настройки SignalR концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="44eff-1087">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1087">Option</span></span> | <span data-ttu-id="44eff-1088">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1088">Default Value</span></span> | <span data-ttu-id="44eff-1089">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="44eff-1090">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-1090">15 seconds</span></span> | <span data-ttu-id="44eff-1091">Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается.</span><span class="sxs-lookup"><span data-stu-id="44eff-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="44eff-1092">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-1093">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="44eff-1094">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-1094">15 seconds</span></span> | <span data-ttu-id="44eff-1095">Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось.</span><span class="sxs-lookup"><span data-stu-id="44eff-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="44eff-1096">При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр.</span><span class="sxs-lookup"><span data-stu-id="44eff-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="44eff-1097">Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="44eff-1098">Все установленные протоколы</span><span class="sxs-lookup"><span data-stu-id="44eff-1098">All installed protocols</span></span> | <span data-ttu-id="44eff-1099">Протоколы, поддерживаемые этим концентратором.</span><span class="sxs-lookup"><span data-stu-id="44eff-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="44eff-1100">По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="44eff-1101">Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="44eff-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="44eff-1102">Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения.</span><span class="sxs-lookup"><span data-stu-id="44eff-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="44eff-1103">Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="44eff-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="44eff-1104">Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="44eff-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="44eff-1105">Дополнительные параметры конфигурации HTTP</span><span class="sxs-lookup"><span data-stu-id="44eff-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="44eff-1106">Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="44eff-1107">Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="44eff-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="44eff-1108">В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44eff-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="44eff-1109">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1109">Option</span></span> | <span data-ttu-id="44eff-1110">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1110">Default Value</span></span> | <span data-ttu-id="44eff-1111">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="44eff-1112">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-1112">32 KB</span></span> | <span data-ttu-id="44eff-1113">Максимальное число байтов, полученных от клиента, который является буфером сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="44eff-1114">Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="44eff-1115">Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub.</span><span class="sxs-lookup"><span data-stu-id="44eff-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="44eff-1116">Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору.</span><span class="sxs-lookup"><span data-stu-id="44eff-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="44eff-1117">32 КБ</span><span class="sxs-lookup"><span data-stu-id="44eff-1117">32 KB</span></span> | <span data-ttu-id="44eff-1118">Максимальное число байтов, отправленных приложением, которые используются в буфере сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="44eff-1119">Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти.</span><span class="sxs-lookup"><span data-stu-id="44eff-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="44eff-1120">Все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-1120">All Transports are enabled.</span></span> | <span data-ttu-id="44eff-1121">Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения.</span><span class="sxs-lookup"><span data-stu-id="44eff-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="44eff-1122">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-1122">See below.</span></span> | <span data-ttu-id="44eff-1123">Дополнительные параметры, относящиеся к длительному транспортному потоку.</span><span class="sxs-lookup"><span data-stu-id="44eff-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="44eff-1124">См. ниже.</span><span class="sxs-lookup"><span data-stu-id="44eff-1124">See below.</span></span> | <span data-ttu-id="44eff-1125">Дополнительные параметры, относящиеся к транспортному протоколу WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="44eff-1126">У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="44eff-1127">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1127">Option</span></span> | <span data-ttu-id="44eff-1128">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1128">Default Value</span></span> | <span data-ttu-id="44eff-1129">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="44eff-1130">90 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-1130">90 seconds</span></span> | <span data-ttu-id="44eff-1131">Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса.</span><span class="sxs-lookup"><span data-stu-id="44eff-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="44eff-1132">Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос.</span><span class="sxs-lookup"><span data-stu-id="44eff-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="44eff-1133">Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:</span><span class="sxs-lookup"><span data-stu-id="44eff-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="44eff-1134">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1134">Option</span></span> | <span data-ttu-id="44eff-1135">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1135">Default Value</span></span> | <span data-ttu-id="44eff-1136">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="44eff-1137">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-1137">5 seconds</span></span> | <span data-ttu-id="44eff-1138">Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано.</span><span class="sxs-lookup"><span data-stu-id="44eff-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="44eff-1139">Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении.</span><span class="sxs-lookup"><span data-stu-id="44eff-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="44eff-1140">Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение.</span><span class="sxs-lookup"><span data-stu-id="44eff-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="44eff-1141">Настройка параметров клиента</span><span class="sxs-lookup"><span data-stu-id="44eff-1141">Configure client options</span></span>

<span data-ttu-id="44eff-1142">Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="44eff-1143">Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="44eff-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="44eff-1144">Настройка журнала</span><span class="sxs-lookup"><span data-stu-id="44eff-1144">Configure logging</span></span>

<span data-ttu-id="44eff-1145">Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода.</span><span class="sxs-lookup"><span data-stu-id="44eff-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="44eff-1146">Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере.</span><span class="sxs-lookup"><span data-stu-id="44eff-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="44eff-1147">Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="44eff-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="44eff-1148">Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты.</span><span class="sxs-lookup"><span data-stu-id="44eff-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="44eff-1149">Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.</span><span class="sxs-lookup"><span data-stu-id="44eff-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="44eff-1150">Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="44eff-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="44eff-1151">Вызовите `AddConsole` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="44eff-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="44eff-1152">В клиенте JavaScript аналогичный `configureLogging` метод существует.</span><span class="sxs-lookup"><span data-stu-id="44eff-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="44eff-1153">Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="44eff-1154">Журналы записываются в окно консоли браузера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="44eff-1155">Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.</span><span class="sxs-lookup"><span data-stu-id="44eff-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="44eff-1156">Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="44eff-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="44eff-1157">SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="44eff-1158">Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="44eff-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="44eff-1159">В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.</span><span class="sxs-lookup"><span data-stu-id="44eff-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="44eff-1160">Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:</span><span class="sxs-lookup"><span data-stu-id="44eff-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="44eff-1161">Это можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="44eff-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="44eff-1162">Настройка разрешенных транспортов</span><span class="sxs-lookup"><span data-stu-id="44eff-1162">Configure allowed transports</span></span>

<span data-ttu-id="44eff-1163">Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="44eff-1164">Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="44eff-1165">По умолчанию все транспорты включены.</span><span class="sxs-lookup"><span data-stu-id="44eff-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="44eff-1166">Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:</span><span class="sxs-lookup"><span data-stu-id="44eff-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="44eff-1167">В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="44eff-1168">Настройка проверки подлинности носителя</span><span class="sxs-lookup"><span data-stu-id="44eff-1168">Configure bearer authentication</span></span>

<span data-ttu-id="44eff-1169">Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="44eff-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="44eff-1170">В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="44eff-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="44eff-1171">В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket).</span><span class="sxs-lookup"><span data-stu-id="44eff-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="44eff-1172">В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .</span><span class="sxs-lookup"><span data-stu-id="44eff-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="44eff-1173">В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="44eff-1174">В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="44eff-1175">В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="44eff-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="44eff-1176">Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="44eff-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="44eff-1177">При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.</span><span class="sxs-lookup"><span data-stu-id="44eff-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="44eff-1178">Настройка времени ожидания и параметров поддержания активности</span><span class="sxs-lookup"><span data-stu-id="44eff-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="44eff-1179">Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:</span><span class="sxs-lookup"><span data-stu-id="44eff-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-1181">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1181">Option</span></span> | <span data-ttu-id="44eff-1182">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1182">Default value</span></span> | <span data-ttu-id="44eff-1183">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="44eff-1184">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-1185">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1185">Timeout for server activity.</span></span> <span data-ttu-id="44eff-1186">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-1187">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-1188">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="44eff-1189">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-1189">15 seconds</span></span> | <span data-ttu-id="44eff-1190">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-1191">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript).</span><span class="sxs-lookup"><span data-stu-id="44eff-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="44eff-1192">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-1193">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="44eff-1194">В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.</span><span class="sxs-lookup"><span data-stu-id="44eff-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="44eff-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-1196">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1196">Option</span></span> | <span data-ttu-id="44eff-1197">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1197">Default value</span></span> | <span data-ttu-id="44eff-1198">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="44eff-1199">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-1200">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1200">Timeout for server activity.</span></span> <span data-ttu-id="44eff-1201">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="44eff-1202">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-1203">Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-1204">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-1205">Параметр</span><span class="sxs-lookup"><span data-stu-id="44eff-1205">Option</span></span> | <span data-ttu-id="44eff-1206">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1206">Default value</span></span> | <span data-ttu-id="44eff-1207">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="44eff-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="44eff-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="44eff-1209">30 секунд (30 000 миллисекунд)</span><span class="sxs-lookup"><span data-stu-id="44eff-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="44eff-1210">Время ожидания активности сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1210">Timeout for server activity.</span></span> <span data-ttu-id="44eff-1211">Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-1212">Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="44eff-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="44eff-1213">Рекомендуемое значение — это число, по крайней мере, двойное `KeepAliveInterval` значение сервера, которое позволяет получить время для проверки связи.</span><span class="sxs-lookup"><span data-stu-id="44eff-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="44eff-1214">15 секунд</span><span class="sxs-lookup"><span data-stu-id="44eff-1214">15 seconds</span></span> | <span data-ttu-id="44eff-1215">Время ожидания для первоначального подтверждения сервера.</span><span class="sxs-lookup"><span data-stu-id="44eff-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="44eff-1216">Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="44eff-1217">Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети.</span><span class="sxs-lookup"><span data-stu-id="44eff-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="44eff-1218">Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="44eff-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="44eff-1219">Настройка дополнительных параметров</span><span class="sxs-lookup"><span data-stu-id="44eff-1219">Configure additional options</span></span>

<span data-ttu-id="44eff-1220">Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:</span><span class="sxs-lookup"><span data-stu-id="44eff-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="44eff-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="44eff-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="44eff-1222">Параметр .NET</span><span class="sxs-lookup"><span data-stu-id="44eff-1222">.NET Option</span></span> |  <span data-ttu-id="44eff-1223">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1223">Default value</span></span> | <span data-ttu-id="44eff-1224">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="44eff-1225">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="44eff-1226">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-1227">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-1228">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="44eff-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1229">Empty</span></span> | <span data-ttu-id="44eff-1230">Коллекция сертификатов TLS для отправки в запросы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44eff-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="44eff-1231">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1231">Empty</span></span> | <span data-ttu-id="44eff-1232">Коллекция HTTP cookie s для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="44eff-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1233">Empty</span></span> | <span data-ttu-id="44eff-1234">Учетные данные для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="44eff-1235">5 с</span><span class="sxs-lookup"><span data-stu-id="44eff-1235">5 seconds</span></span> | <span data-ttu-id="44eff-1236">Только WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-1236">WebSockets only.</span></span> <span data-ttu-id="44eff-1237">Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие.</span><span class="sxs-lookup"><span data-stu-id="44eff-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="44eff-1238">Если сервер не подтверждает закрытие в течение этого времени, клиент отключается.</span><span class="sxs-lookup"><span data-stu-id="44eff-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="44eff-1239">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1239">Empty</span></span> | <span data-ttu-id="44eff-1240">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="44eff-1241">Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="44eff-1242">Не используется для соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="44eff-1243">Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра.</span><span class="sxs-lookup"><span data-stu-id="44eff-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="44eff-1244">Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр.</span><span class="sxs-lookup"><span data-stu-id="44eff-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="44eff-1245">**При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.**</span><span class="sxs-lookup"><span data-stu-id="44eff-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="44eff-1246">HTTP-прокси, используемый при отправке HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="44eff-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="44eff-1247">Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets.</span><span class="sxs-lookup"><span data-stu-id="44eff-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="44eff-1248">Это позволяет использовать проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="44eff-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="44eff-1249">Делегат, который можно использовать для настройки дополнительных параметров WebSocket.</span><span class="sxs-lookup"><span data-stu-id="44eff-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="44eff-1250">Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="44eff-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="44eff-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="44eff-1252">Параметр JavaScript</span><span class="sxs-lookup"><span data-stu-id="44eff-1252">JavaScript Option</span></span> | <span data-ttu-id="44eff-1253">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1253">Default Value</span></span> | <span data-ttu-id="44eff-1254">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="44eff-1255">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="44eff-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения.</span><span class="sxs-lookup"><span data-stu-id="44eff-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="44eff-1257">Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="44eff-1258">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-1259">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-1260">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="44eff-1261">Java</span><span class="sxs-lookup"><span data-stu-id="44eff-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="44eff-1262">Параметр Java</span><span class="sxs-lookup"><span data-stu-id="44eff-1262">Java Option</span></span> | <span data-ttu-id="44eff-1263">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44eff-1263">Default Value</span></span> | <span data-ttu-id="44eff-1264">Описание</span><span class="sxs-lookup"><span data-stu-id="44eff-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="44eff-1265">Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="44eff-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="44eff-1266">Установите этот параметр в значение `true` , чтобы пропустить шаг согласования.</span><span class="sxs-lookup"><span data-stu-id="44eff-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="44eff-1267">**Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**.</span><span class="sxs-lookup"><span data-stu-id="44eff-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="44eff-1268">Этот параметр нельзя включить при использовании SignalR службы Azure.</span><span class="sxs-lookup"><span data-stu-id="44eff-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="44eff-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="44eff-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="44eff-1270">Empty</span><span class="sxs-lookup"><span data-stu-id="44eff-1270">Empty</span></span> | <span data-ttu-id="44eff-1271">Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="44eff-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="44eff-1272">В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="44eff-1273">В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="44eff-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="44eff-1274">В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="44eff-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="44eff-1275">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44eff-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
