---
title: Поддержка WebSockets в ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с WebSocket в ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 6edf2017cc889321cfb484e643b75711fd66004d
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058354"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="1ffa3-103">Поддержка WebSockets в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1ffa3-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="1ffa3-104">Авторы: [Tom Dykstra](https://github.com/tdykstra) (Том Дикстра) и [Andrew Stanton-Nurse](https://github.com/anurse) (Эндрю Стэнтон-Нёрс)</span><span class="sxs-lookup"><span data-stu-id="1ffa3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="1ffa3-105">Эта статья описывает начало работы с WebSocket в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="1ffa3-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) — это протокол, предоставляющий сохраняемые двусторонние каналы связи по TCP-подключениям.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="1ffa3-107">Он используется в приложениях, где нужна быстрая связь в режиме реального времени, например в чатах, панелях мониторинга и играх.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="1ffa3-108">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="1ffa3-109">[Процедура выполнения](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="1ffa3-110">[ASP.NET Core SignalR](xref:signalr/introduction) — это библиотека, которая упрощает добавление веб-функций в режиме реального времени в приложения.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="1ffa3-111">Она использует WebSocket, когда это возможно.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="1ffa3-112">Для большинства приложений рекомендуется использовать SignalR вместо прямых соединений WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="1ffa3-113">Служба SignalR обеспечивает резервный транспорт для сред, в которых соединения WebSocket недоступны.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="1ffa3-114">Она также предоставляет простую модель приложений на основе удаленного вызова процедур.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="1ffa3-115">В большинстве сценариев SignalR по производительности не уступает прямым соединениям WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="1ffa3-116">Для некоторых приложений [gRPC в .NET](xref:grpc/index) предоставляет альтернативу WebSockets.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1ffa3-117">Обязательные условия</span><span class="sxs-lookup"><span data-stu-id="1ffa3-117">Prerequisites</span></span>

* <span data-ttu-id="1ffa3-118">Любая ОС с поддержкой ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="1ffa3-119">Windows 7/Windows Server 2008 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="1ffa3-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="1ffa3-120">Linux</span><span class="sxs-lookup"><span data-stu-id="1ffa3-120">Linux</span></span>
  * <span data-ttu-id="1ffa3-121">macOS</span><span class="sxs-lookup"><span data-stu-id="1ffa3-121">macOS</span></span>  
* <span data-ttu-id="1ffa3-122">Если приложение выполняется в Windows с IIS:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="1ffa3-123">Windows 8/Windows Server 2012 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="1ffa3-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="1ffa3-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="1ffa3-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="1ffa3-125">WebSockets должен быть включен.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-125">WebSockets must be enabled.</span></span> <span data-ttu-id="1ffa3-126">См. раздел [Поддержка служб IIS/IIS Express](#iisiis-express-support).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="1ffa3-127">Если приложение выполняется в [HTTP.sys](xref:fundamentals/servers/httpsys):</span><span class="sxs-lookup"><span data-stu-id="1ffa3-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="1ffa3-128">Windows 8/Windows Server 2012 или более поздних версий</span><span class="sxs-lookup"><span data-stu-id="1ffa3-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="1ffa3-129">Список поддерживаемых обозревателей см. на странице https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="1ffa3-130">Настройка ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="1ffa3-130">Configure the middleware</span></span>

<span data-ttu-id="1ffa3-131">Добавьте ПО промежуточного слоя WebSocket в метод `Configure` класса `Startup`:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1ffa3-132">Можно настроить следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-132">The following settings can be configured:</span></span>

* <span data-ttu-id="1ffa3-133">`KeepAliveInterval` — как часто нужно отправлять клиенту кадры проверки связи, чтобы прокси-серверы удерживали соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="1ffa3-134">Значение по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-134">The default is two minutes.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1ffa3-135">Можно настроить следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-135">The following settings can be configured:</span></span>

* <span data-ttu-id="1ffa3-136">`KeepAliveInterval` — как часто нужно отправлять клиенту кадры проверки связи, чтобы прокси-серверы удерживали соединение открытым.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="1ffa3-137">Значение по умолчанию — две минуты.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-137">The default is two minutes.</span></span>
* <span data-ttu-id="1ffa3-138">`AllowedOrigins` — список допустимых значений заголовка Origin для запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-138">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="1ffa3-139">По умолчанию разрешены все источники.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-139">By default, all origins are allowed.</span></span> <span data-ttu-id="1ffa3-140">Дополнительные сведения см. в разделе "Ограничения для источников WebSocket" ниже.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-140">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="1ffa3-141">Принятие запросов WebSocket</span><span class="sxs-lookup"><span data-stu-id="1ffa3-141">Accept WebSocket requests</span></span>

<span data-ttu-id="1ffa3-142">На более позднем этапе жизненного цикла запроса (например, далее в методе `Configure` или методе действия) проверьте, относится ли запрос к WebSocket, и примите его.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-142">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="1ffa3-143">Следующий пример взят из дальнейшей части метода `Configure`:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-143">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="1ffa3-144">Запрос WebSocket может поступить по любому URL-адресу, но этот пример кода принимает только запросы для `/ws`.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-144">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="1ffa3-145">При использовании WebSocket вам **необходимо** поддерживать работу конвейера ПО промежуточного слоя, пока соединение активно.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-145">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="1ffa3-146">При попытке отправить или получить сообщение WebSocket после того, как конвейер ПО промежуточного слоя завершил работу, вы можете получить исключение такого вида:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-146">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="1ffa3-147">Если вы используете фоновую службу для записи данных в WebSocket, убедитесь, что конвейер ПО промежуточного слоя продолжает работу.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-147">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="1ffa3-148">Это можно сделать с помощью <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-148">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="1ffa3-149">Передайте `TaskCompletionSource` фоновой службе, а после завершения работы с WebSocket вызовите <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> с ее помощью.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-149">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="1ffa3-150">Затем используйте конструкцию `await` для ожидания свойства <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> во время запроса, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-150">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="1ffa3-151">Исключение закрытия WebSocket может также возникнуть, если метод действия возвращает управление слишком быстро.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-151">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="1ffa3-152">При принятии сокета в методе действия дождитесь завершения выполнения кода, в котором используется сокет, прежде чем возвращать управление из метода действия.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-152">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="1ffa3-153">Никогда не используйте `Task.Wait`, `Task.Result` или аналогичные блокирующие вызовы для ожидания выполнения сокета, так как это может привести к серьезным проблемам потокового выполнения.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-153">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="1ffa3-154">Всегда используйте `await`.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-154">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="1ffa3-155">Отправка и получение сообщений</span><span class="sxs-lookup"><span data-stu-id="1ffa3-155">Send and receive messages</span></span>

<span data-ttu-id="1ffa3-156">Метод `AcceptWebSocketAsync` обновляет TCP-соединение до соединения WebSocket и предоставляет объект [WebSocket](/dotnet/core/api/system.net.websockets.websocket).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-156">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="1ffa3-157">Используйте объект `WebSocket` для отправки и получения сообщений.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-157">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="1ffa3-158">Приведенный выше код, который принимает запрос WebSocket, передает объект `WebSocket` в метод `Echo`.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-158">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="1ffa3-159">Код принимает сообщение и сразу отправляет такое же сообщение обратно.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-159">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="1ffa3-160">Сообщения отправляются и получаются циклически, пока клиент не закроет подключение:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-160">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="1ffa3-161">Если вы принимаете подключение WebSocket до начала этого цикла, конвейер ПО промежуточного слоя завершается.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-161">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="1ffa3-162">После закрытия сокета конвейер развертывается.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-162">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="1ffa3-163">То есть запрос перестает перемещаться по конвейеру после принятия WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-163">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="1ffa3-164">После завершения цикла и закрытия сокета запрос возвращается в конвейер.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-164">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="1ffa3-165">Обработка отключений клиента</span><span class="sxs-lookup"><span data-stu-id="1ffa3-165">Handle client disconnects</span></span>

<span data-ttu-id="1ffa3-166">При отключении клиента из-за потери связи сервер не получает сведения автоматически.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-166">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="1ffa3-167">Сервер получает сообщение об отключении, только если клиент отправляет его. В случае потери подключения к Интернету это невозможно.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-167">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="1ffa3-168">Если при этом вы хотите выполнить определенное действие, установите время ожидания сигнала клиента с определенным интервалом.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-168">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="1ffa3-169">Если клиент не всегда отправляет сообщения, а вы не хотите ожидать истечения времени ожидания только потому, что подключение не используется, тогда клиент может использовать таймер для отправки сообщения проверки связи каждые X секунд.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-169">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="1ffa3-170">Если сообщение не было получено на сервере в течение 2\*X секунд после предыдущего, завершите подключение и сообщите о том, что клиент отключен.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-170">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="1ffa3-171">Подождите вдвое дольше ожидаемого временного интервала, чтобы оставить дополнительное время на задержки в сети при отправке сообщения проверки связи.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-171">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="1ffa3-172">Ограничения для источников WebSocket</span><span class="sxs-lookup"><span data-stu-id="1ffa3-172">WebSocket origin restriction</span></span>

<span data-ttu-id="1ffa3-173">Варианты защиты, предоставляемые CORS, не применяются к WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-173">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="1ffa3-174">Браузеры **не** поддерживают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-174">Browsers do **not**:</span></span>

* <span data-ttu-id="1ffa3-175">выполнение предварительных запросов CORS;</span><span class="sxs-lookup"><span data-stu-id="1ffa3-175">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="1ffa3-176">использование ограничений, указанных в заголовках `Access-Control`, при выполнении запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-176">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="1ffa3-177">Однако браузеры отправляют заголовок `Origin` при выпуске запросов WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-177">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="1ffa3-178">Приложения должны быть настроены для проверки этих заголовков, чтобы использовались только WebSocket из ожидаемых источников.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-178">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="1ffa3-179">Если вы размещаете сервер по адресу "https://server.com", а клиент — по адресу "https://client.com", добавьте "https://client.com" в список `AllowedOrigins` подлежащих проверке WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-179">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="1ffa3-180">Заголовок `Origin` контролируется клиентом и, как и заголовок `Referer`, может быть подделан.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-180">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="1ffa3-181">**Не** используйте эти заголовки в качестве механизма проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-181">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="1ffa3-182">Поддержка служб IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="1ffa3-182">IIS/IIS Express support</span></span>

<span data-ttu-id="1ffa3-183">Windows Server 2012 или более поздней версии и Windows 8 или более поздней версии с IIS и IIS Express 8 или более поздней версии поддерживают протокол WebSocket.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-183">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="1ffa3-184">Соединения WebSockets всегда включены при использовании IIS Express.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-184">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="1ffa3-185">Включение WebSockets в службах IIS</span><span class="sxs-lookup"><span data-stu-id="1ffa3-185">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="1ffa3-186">Чтобы включить поддержку протокола WebSocket в Windows Server 2012 или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-186">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="1ffa3-187">Эти действия не требуется выполнять при использовании IIS Express</span><span class="sxs-lookup"><span data-stu-id="1ffa3-187">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="1ffa3-188">В меню **Управление** запустите мастер **Добавить роли и компоненты** или в окне **Диспетчер серверов** щелкните соответствующую ссылку.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-188">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="1ffa3-189">Выберите **Установка ролей или компонентов**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-189">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="1ffa3-190">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-190">Select **Next**.</span></span>
1. <span data-ttu-id="1ffa3-191">Выберите подходящий сервер (по умолчанию выбирается локальный сервер).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-191">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="1ffa3-192">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-192">Select **Next**.</span></span>
1. <span data-ttu-id="1ffa3-193">Разверните **Веб-сервер (IIS)** в дереве **Роли**, разверните **Веб-сервер**, а затем **Разработка приложений**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-193">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="1ffa3-194">Выберите **протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-194">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="1ffa3-195">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-195">Select **Next**.</span></span>
1. <span data-ttu-id="1ffa3-196">Если дополнительные функции не требуются, нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-196">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="1ffa3-197">Нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-197">Select **Install**.</span></span>
1. <span data-ttu-id="1ffa3-198">По завершении установки выберите **Закрыть**, чтобы выйти из мастера.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-198">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="1ffa3-199">Чтобы включить поддержку протокола WebSocket в Windows 8 или более поздней версии:</span><span class="sxs-lookup"><span data-stu-id="1ffa3-199">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="1ffa3-200">Эти действия не требуется выполнять при использовании IIS Express</span><span class="sxs-lookup"><span data-stu-id="1ffa3-200">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="1ffa3-201">Последовательно выберите **Панель управления** > **Программы** > **Программы и компоненты** > **Включение или отключение компонентов Windows** (в левой части экрана).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-201">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="1ffa3-202">Откройте следующие узлы: **IIS** > **Службы Интернета** > **Компоненты разработки приложений**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-202">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="1ffa3-203">Выберите компонент **Протокол WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-203">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="1ffa3-204">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-204">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="1ffa3-205">Отключите WebSocket при использовании socket.io на Node.js</span><span class="sxs-lookup"><span data-stu-id="1ffa3-205">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="1ffa3-206">Если используется поддержка WebSocket в [socket.io](https://socket.io/) на [Node.js](https://nodejs.org/), отключите модуль WebSocket IIS по умолчанию с помощью элемента `webSocket` в *web.config* или *applicationHost.config*. Если не выполнить этот шаг, модуль IIS WebSocket попытается обработать соединение WebSocket, а не Node.js и приложение.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-206">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="1ffa3-207">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="1ffa3-207">Sample app</span></span>

<span data-ttu-id="1ffa3-208">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) в этой статье — это эхо-приложение.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-208">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="1ffa3-209">Оно имеет веб-страницу, которая устанавливает соединения WebSocket, а сервер перенаправляет все полученные сообщения обратно клиенту.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-209">It has a webpage that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="1ffa3-210">Этот пример приложения не настроен для запуска из Visual Studio с IIS Express, поэтому его необходимо запустить в командной оболочке с [`dotnet run`](/dotnet/core/tools/dotnet-run) и затем перейти в браузере по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-210">The sample app isn't configured to run from Visual Studio with IIS Express, so run the app in a command shell with [`dotnet run`](/dotnet/core/tools/dotnet-run) and navigate in a browser to `http://localhost:5000`.</span></span> <span data-ttu-id="1ffa3-211">На этой веб-странице отображается состояние подключения.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-211">The webpage shows the connection status:</span></span>

![Начальное состояние веб-страницы до подключения WebSocket](websockets/_static/start.png)

<span data-ttu-id="1ffa3-213">Выберите **Connect** (Подключить), чтобы отправить запрос WebSocket на показанный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-213">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="1ffa3-214">Введите тестовое сообщение и выберите **Send** (Отправить).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-214">Enter a test message and select **Send**.</span></span> <span data-ttu-id="1ffa3-215">После этого выберите **Close Socket** (Закрыть сокет).</span><span class="sxs-lookup"><span data-stu-id="1ffa3-215">When done, select **Close Socket**.</span></span> <span data-ttu-id="1ffa3-216">В разделе **Communication Log** (Журнал связи) выводится каждое выполняемое действие открытия, отправки и закрытия.</span><span class="sxs-lookup"><span data-stu-id="1ffa3-216">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Конечное состояние веб-страницы после подключения WebSocket и отправки тестовых сообщений с их последующим приемом](websockets/_static/end.png)
