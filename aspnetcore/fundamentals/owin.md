---
title: Открытый веб-интерфейс для .NET (OWIN) в ASP.NET Core
author: ardalis
description: Сведения о том, как ASP.NET Core поддерживает открытый веб-интерфейс для .NET (OWIN), позволяющий ослабить связь веб-приложений с веб-серверами.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975278"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="51378-103">Открытый веб-интерфейс для .NET (OWIN) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51378-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="51378-104">Авторы: [Стив Смит (Steve Smith)](https://ardalis.com/) и [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="51378-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51378-105">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="51378-105">ASP.NET Core:</span></span>

* <span data-ttu-id="51378-106">поддерживает открытый веб-интерфейс для .NET (OWIN);</span><span class="sxs-lookup"><span data-stu-id="51378-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="51378-107">имеет совместимые с .NET Core замены для библиотек `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)).</span><span class="sxs-lookup"><span data-stu-id="51378-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="51378-108">OWIN позволяет ослабить зависимость веб-приложений от веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="51378-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="51378-109">Он определяет стандартный способ использования ПО промежуточного в конвейере для обработки запросов и связанных с ними откликов.</span><span class="sxs-lookup"><span data-stu-id="51378-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="51378-110">Приложения ASP.NET Core и ПО промежуточного слоя могут взаимодействовать с приложениями, серверами и ПО промежуточного слоя, основанными на OWIN.</span><span class="sxs-lookup"><span data-stu-id="51378-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="51378-111">OWIN обеспечивает разделительный уровень, позволяющий совместно использовать две платформы с разнородными объектными моделями.</span><span class="sxs-lookup"><span data-stu-id="51378-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="51378-112">Пакет `Microsoft.AspNetCore.Owin` содержит две реализации адаптера:</span><span class="sxs-lookup"><span data-stu-id="51378-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="51378-113">ASP.NET Core в OWIN</span><span class="sxs-lookup"><span data-stu-id="51378-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="51378-114">OWIN в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51378-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="51378-115">Это позволяет размещать ASP.NET Core поверх сервера или узла, совместимого с OWIN, а также запускать другие совместимые с OWIN компоненты поверх ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51378-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="51378-116">Использование этих адаптеров сопряжено с потерями производительности.</span><span class="sxs-lookup"><span data-stu-id="51378-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="51378-117">Приложениям, использующим только компоненты ASP.NET Core, не следует использовать адаптеры или пакет `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="51378-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="51378-118">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="51378-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="51378-119">Выполнение ПО промежуточного слоя OWIN в конвейере ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="51378-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="51378-120">Поддержка OWIN для ASP.NET Core развертывается в составе пакета `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="51378-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="51378-121">Вы можете импортировать поддержку OWIN в проект, установив этот пакет.</span><span class="sxs-lookup"><span data-stu-id="51378-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="51378-122">ПО промежуточного слоя OWIN соответствует [спецификации OWIN](https://owin.org/spec/spec/owin-1.0.0.html), где требуется задать интерфейс `Func<IDictionary<string, object>, Task>` и определенные ключи (такие как `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="51378-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="51378-123">Следующее простое ПО промежуточного слоя OWIN отображает сообщение "Hello World":</span><span class="sxs-lookup"><span data-stu-id="51378-123">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="51378-124">Сигнатура примера возвращает `Task` и принимает `IDictionary<string, object>`, как того требует OWIN.</span><span class="sxs-lookup"><span data-stu-id="51378-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="51378-125">Приведенный ниже код показывает, как добавить ПО промежуточного слоя `OwinHello` (показано выше) в конвейер ASP.NET Core с помощью метода расширения `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="51378-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="51378-126">В конвейере OWIN можно настроить и другие действия.</span><span class="sxs-lookup"><span data-stu-id="51378-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="51378-127">Заголовки отклика можно изменять только до первой записи в поток отклика.</span><span class="sxs-lookup"><span data-stu-id="51378-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="51378-128">Из соображений производительности не рекомендуется выполнять несколько вызовов `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="51378-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="51378-129">Компоненты OWIN лучше всего работают, когда сгруппированы друг с другом.</span><span class="sxs-lookup"><span data-stu-id="51378-129">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="51378-130">Запуск ASP.NET Core на основанном на OWIN сервере и использование его поддержки WebSocket</span><span class="sxs-lookup"><span data-stu-id="51378-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="51378-131">Другим примером того, как ASP.NET Core может использовать функции сервера на основе OWIN, является доступ к таким функциям, как WebSocket.</span><span class="sxs-lookup"><span data-stu-id="51378-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="51378-132">Веб-сервер OWIN .NET, используемый в предыдущем примере, имеет встроенную поддержку веб-сокетов, что может использовать приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="51378-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="51378-133">Приведенный ниже пример показывает простое веб-приложение, которое поддерживает веб-сокеты и выводит обратно все данные, отправляемые на сервер через WebSocket.</span><span class="sxs-lookup"><span data-stu-id="51378-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="51378-134">Среда OWIN</span><span class="sxs-lookup"><span data-stu-id="51378-134">OWIN environment</span></span>

<span data-ttu-id="51378-135">Вы можете создать среду OWIN с помощью `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="51378-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="51378-136">Ключи OWIN</span><span class="sxs-lookup"><span data-stu-id="51378-136">OWIN keys</span></span>

<span data-ttu-id="51378-137">OWIN использует объект `IDictionary<string,object>` для передачи сведений через систему обмена запросами и откликами HTTP.</span><span class="sxs-lookup"><span data-stu-id="51378-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="51378-138">ASP.NET Core реализует указанные ниже ключи.</span><span class="sxs-lookup"><span data-stu-id="51378-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="51378-139">См. [основную спецификацию, расширения](https://owin.org/#spec), а также статью [Рекомендации по ключам OWIN и общие ключи](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="51378-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="51378-140">Данные запроса (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="51378-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="51378-141">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-141">Key</span></span>               | <span data-ttu-id="51378-142">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-142">Value (type)</span></span> | <span data-ttu-id="51378-143">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-144">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="51378-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="51378-145">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="51378-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="51378-146">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="51378-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="51378-147">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="51378-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="51378-148">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="51378-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="51378-149">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="51378-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="51378-150">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="51378-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="51378-151">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="51378-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="51378-152">Данные запроса (OWIN версии 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="51378-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="51378-153">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-153">Key</span></span>               | <span data-ttu-id="51378-154">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-154">Value (type)</span></span> | <span data-ttu-id="51378-155">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-156">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="51378-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="51378-157">Необязательный</span><span class="sxs-lookup"><span data-stu-id="51378-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="51378-158">Данные отклика (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="51378-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="51378-159">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-159">Key</span></span>               | <span data-ttu-id="51378-160">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-160">Value (type)</span></span> | <span data-ttu-id="51378-161">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-162">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="51378-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="51378-163">Необязательный</span><span class="sxs-lookup"><span data-stu-id="51378-163">Optional</span></span> |
| <span data-ttu-id="51378-164">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="51378-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="51378-165">Необязательный</span><span class="sxs-lookup"><span data-stu-id="51378-165">Optional</span></span> |
| <span data-ttu-id="51378-166">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="51378-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="51378-167">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="51378-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="51378-168">Другие данные (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="51378-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="51378-169">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-169">Key</span></span>               | <span data-ttu-id="51378-170">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-170">Value (type)</span></span> | <span data-ttu-id="51378-171">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-172">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="51378-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="51378-173">owin.Version</span><span class="sxs-lookup"><span data-stu-id="51378-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="51378-174">Общие ключи</span><span class="sxs-lookup"><span data-stu-id="51378-174">Common keys</span></span>

| <span data-ttu-id="51378-175">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-175">Key</span></span>               | <span data-ttu-id="51378-176">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-176">Value (type)</span></span> | <span data-ttu-id="51378-177">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-178">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="51378-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="51378-179">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="51378-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="51378-180">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="51378-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="51378-181">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="51378-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="51378-182">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="51378-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="51378-183">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="51378-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="51378-184">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="51378-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="51378-185">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="51378-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="51378-186">SendFiles версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="51378-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="51378-187">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-187">Key</span></span>               | <span data-ttu-id="51378-188">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-188">Value (type)</span></span> | <span data-ttu-id="51378-189">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-190">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="51378-190">sendfile.SendAsync</span></span> | <span data-ttu-id="51378-191">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="51378-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="51378-192">Для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="51378-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="51378-193">Opaque версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="51378-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="51378-194">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-194">Key</span></span>               | <span data-ttu-id="51378-195">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-195">Value (type)</span></span> | <span data-ttu-id="51378-196">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-197">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="51378-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="51378-198">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="51378-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="51378-199">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="51378-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="51378-200">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="51378-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="51378-201">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="51378-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="51378-202">WebSocket версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="51378-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="51378-203">Ключ</span><span class="sxs-lookup"><span data-stu-id="51378-203">Key</span></span>               | <span data-ttu-id="51378-204">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="51378-204">Value (type)</span></span> | <span data-ttu-id="51378-205">Описание</span><span class="sxs-lookup"><span data-stu-id="51378-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="51378-206">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="51378-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="51378-207">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="51378-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="51378-208">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="51378-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="51378-209">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="51378-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="51378-210">Нет в спецификации</span><span class="sxs-lookup"><span data-stu-id="51378-210">Non-spec</span></span> |
| <span data-ttu-id="51378-211">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="51378-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="51378-212">См. [раздел 4.2.2 RFC6455](https://tools.ietf.org/html/rfc6455#section-4.2.2), шаг 5.5</span><span class="sxs-lookup"><span data-stu-id="51378-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="51378-213">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="51378-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="51378-214">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="51378-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="51378-215">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="51378-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="51378-216">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="51378-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="51378-217">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="51378-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="51378-218">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="51378-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="51378-219">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="51378-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="51378-220">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="51378-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="51378-221">Необязательный</span><span class="sxs-lookup"><span data-stu-id="51378-221">Optional</span></span> |
| <span data-ttu-id="51378-222">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="51378-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="51378-223">Необязательный</span><span class="sxs-lookup"><span data-stu-id="51378-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="51378-224">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="51378-224">Additional resources</span></span>

* [<span data-ttu-id="51378-225">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="51378-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="51378-226">Серверы</span><span class="sxs-lookup"><span data-stu-id="51378-226">Servers</span></span>](xref:fundamentals/servers/index)
