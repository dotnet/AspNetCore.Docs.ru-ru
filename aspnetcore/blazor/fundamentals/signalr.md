---
title: Руководство по ASP.NET Core Blazor SignalR
author: guardrex
description: Сведения о настройке подключений Blazor SignalR и управлении ими.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279864"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="c64ba-103">Руководство по ASP.NET Core Blazor SignalR</span><span class="sxs-lookup"><span data-stu-id="c64ba-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="c64ba-104">Общие рекомендации по настройке ASP.NET Core SignalR см. в разделах в области <xref:signalr/introduction> документации.</span><span class="sxs-lookup"><span data-stu-id="c64ba-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="c64ba-105">Сведения о настройке SignalR, [добавленной в размещенное решениеBlazor WebAssembly](xref:tutorials/signalr-blazor), см. в разделе <xref:signalr/configuration#configure-server-options>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="c64ba-106">Параметры обработчика цепи</span><span class="sxs-lookup"><span data-stu-id="c64ba-106">Circuit handler options</span></span>

<span data-ttu-id="c64ba-107">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c64ba-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c64ba-108">Настройте цепь Blazor Server, используя <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>, как показано в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="c64ba-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="c64ba-109">Параметр</span><span class="sxs-lookup"><span data-stu-id="c64ba-109">Option</span></span> | <span data-ttu-id="c64ba-110">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="c64ba-110">Default</span></span> | <span data-ttu-id="c64ba-111">Описание</span><span class="sxs-lookup"><span data-stu-id="c64ba-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="c64ba-112">Отправка подробных сообщений об исключениях в JavaScript, когда в цепи возникает необработанное исключение или если вызов метода .NET через взаимодействие с JS приводит к возникновению исключения.</span><span class="sxs-lookup"><span data-stu-id="c64ba-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="c64ba-113">100</span><span class="sxs-lookup"><span data-stu-id="c64ba-113">100</span></span> | <span data-ttu-id="c64ba-114">Максимальное число отключенных каналов, которые заданный сервер удерживает в памяти за один раз.</span><span class="sxs-lookup"><span data-stu-id="c64ba-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="c64ba-115">3 минуты</span><span class="sxs-lookup"><span data-stu-id="c64ba-115">3 minutes</span></span> | <span data-ttu-id="c64ba-116">Максимальное время, в течение которого отключенный канал удерживается в памяти, прежде чем будет сброшен.</span><span class="sxs-lookup"><span data-stu-id="c64ba-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="c64ba-117">1 минута</span><span class="sxs-lookup"><span data-stu-id="c64ba-117">1 minute</span></span> | <span data-ttu-id="c64ba-118">Максимальное время ожидания сервера до истечения времени ожидания асинхронного вызова функции JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c64ba-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="c64ba-119">10</span><span class="sxs-lookup"><span data-stu-id="c64ba-119">10</span></span> | <span data-ttu-id="c64ba-120">Максимальное число неподтвержденных пакетов преобразования для просмотра, которые сервер хранит в памяти на канал в указанное время для поддержки надежного повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="c64ba-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="c64ba-121">После достижения предела сервер прекращает создавать новые пакеты преобразования для просмотра, пока один или несколько пакетов не будут подтверждены клиентом.</span><span class="sxs-lookup"><span data-stu-id="c64ba-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="c64ba-122">Настройте параметры в `Startup.ConfigureServices` с помощью делегата параметров для <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="c64ba-123">В следующем примере присваиваются значения параметров по умолчанию, показанные в предыдущей таблице.</span><span class="sxs-lookup"><span data-stu-id="c64ba-123">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="c64ba-124">Чтобы настроить <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, используйте <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> с <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="c64ba-125">Описания параметров см. в разделе <xref:signalr/configuration#configure-server-options>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="c64ba-126">В следующем примере присваиваются значения параметров по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="c64ba-126">The following example assigns the default option values:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="c64ba-127">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="c64ba-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="c64ba-128">*Этот раздел относится к Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="c64ba-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="c64ba-129">Чтобы настроить базовый клиент SignalR для отправки учетных данных, таких как файлы cookie или заголовки проверки подлинности HTTP:</span><span class="sxs-lookup"><span data-stu-id="c64ba-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="c64ba-130">используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, чтобы задать <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> в независящих от источника запросах [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch):</span><span class="sxs-lookup"><span data-stu-id="c64ba-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="c64ba-131">присвойте <xref:System.Net.Http.HttpMessageHandler> параметру <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>:</span><span class="sxs-lookup"><span data-stu-id="c64ba-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="c64ba-132">Для получения дополнительной информации см. <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c64ba-133">Отражение состояния соединения в пользовательском интерфейсе</span><span class="sxs-lookup"><span data-stu-id="c64ba-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c64ba-134">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c64ba-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c64ba-135">Когда клиент обнаруживает, что соединение было потеряно, пользовательский интерфейс по умолчанию отображается пользователю, в то время как клиент пытается восстановить соединение.</span><span class="sxs-lookup"><span data-stu-id="c64ba-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c64ba-136">Если происходит повторный сбой подключения, пользователю предоставляется возможность повторить попытку.</span><span class="sxs-lookup"><span data-stu-id="c64ba-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c64ba-137">Чтобы настроить пользовательский интерфейс, определите значение `id` элемента `components-reconnect-modal` в блоке `<body>` страницы Razor `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c64ba-138">Добавьте следующее в таблицу стилей приложения (`wwwroot/css/app.css` или `wwwroot/css/site.css`):</span><span class="sxs-lookup"><span data-stu-id="c64ba-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="c64ba-139">В следующей таблице описаны классы CSS, применяемые к элементу `components-reconnect-modal`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="c64ba-140">Класс CSS</span><span class="sxs-lookup"><span data-stu-id="c64ba-140">CSS class</span></span>                       | <span data-ttu-id="c64ba-141">Означает&hellip;</span><span class="sxs-lookup"><span data-stu-id="c64ba-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="c64ba-142">Потеря соединения.</span><span class="sxs-lookup"><span data-stu-id="c64ba-142">A lost connection.</span></span> <span data-ttu-id="c64ba-143">Клиент пытается повторно подключиться.</span><span class="sxs-lookup"><span data-stu-id="c64ba-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="c64ba-144">Отобразить модальное окно.</span><span class="sxs-lookup"><span data-stu-id="c64ba-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="c64ba-145">Активное подключение к серверу восстановлено.</span><span class="sxs-lookup"><span data-stu-id="c64ba-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="c64ba-146">Скрыть модальное окно.</span><span class="sxs-lookup"><span data-stu-id="c64ba-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="c64ba-147">Сбой повторного подключения, возможно, из-за сбоя сети.</span><span class="sxs-lookup"><span data-stu-id="c64ba-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c64ba-148">Чтобы повторить попытку подключения, вызовите метод `window.Blazor.reconnect()`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="c64ba-149">Повторное подключение отклонено.</span><span class="sxs-lookup"><span data-stu-id="c64ba-149">Reconnection rejected.</span></span> <span data-ttu-id="c64ba-150">Сервер был достигнут, но отклонил подключение, и состояние пользователя на сервере потеряно.</span><span class="sxs-lookup"><span data-stu-id="c64ba-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c64ba-151">Чтобы перезагрузить приложение, вызовите метод `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="c64ba-152">Это состояние соединения может появиться в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="c64ba-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="c64ba-153">произошел сбой в цепи на стороне сервера;</span><span class="sxs-lookup"><span data-stu-id="c64ba-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c64ba-154">клиент был отключен достаточно долго, чтобы сервер сбросил состояние пользователя.</span><span class="sxs-lookup"><span data-stu-id="c64ba-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c64ba-155">Экземпляры компонентов, с которыми взаимодействовал пользователь, удаляются;</span><span class="sxs-lookup"><span data-stu-id="c64ba-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="c64ba-156">сервер был перезагружен или был выполнен перезапуск рабочего процесса приложения.</span><span class="sxs-lookup"><span data-stu-id="c64ba-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="c64ba-157">Режим обработки</span><span class="sxs-lookup"><span data-stu-id="c64ba-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c64ba-158">*Этот раздел относится к размещенным Blazor WebAssembly и Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c64ba-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="c64ba-159">Приложения Blazor по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере.</span><span class="sxs-lookup"><span data-stu-id="c64ba-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="c64ba-160">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c64ba-161">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c64ba-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c64ba-162">Приложения Blazor Server по умолчанию настроены на предварительную отрисовку пользовательского интерфейса на сервере, прежде чем будет установлено подключение клиента с сервером.</span><span class="sxs-lookup"><span data-stu-id="c64ba-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c64ba-163">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="c64ba-164">Инициализация канала Blazor</span><span class="sxs-lookup"><span data-stu-id="c64ba-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="c64ba-165">*Этот раздел относится к Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="c64ba-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="c64ba-166">Настройте ручной запуск [ канала SignalR](xref:blazor/hosting-models#circuits) приложения Blazor Server в файле `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="c64ba-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="c64ba-167">добавьте атрибут `autostart="false"` в тег `<script>` для сценария `blazor.server.js`;</span><span class="sxs-lookup"><span data-stu-id="c64ba-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c64ba-168">Разместите скрипт, который вызывает `Blazor.start` после тега скрипта `blazor.server.js` и внутри закрывающего тега `</body>`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="c64ba-169">При отключении `autostart` все части приложения, которые не зависят от канала, работают нормально.</span><span class="sxs-lookup"><span data-stu-id="c64ba-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="c64ba-170">Например, работает маршрутизация на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="c64ba-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="c64ba-171">Однако все аспекты, зависящие от канала, не будут работать до вызова `Blazor.start`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="c64ba-172">Пока не будет установлен канал, поведение приложения будет непредсказуемым.</span><span class="sxs-lookup"><span data-stu-id="c64ba-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="c64ba-173">Например, когда канал отключен, методы компонентов не выполняются.</span><span class="sxs-lookup"><span data-stu-id="c64ba-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="c64ba-174">Инициализация Blazor при готовности документа</span><span class="sxs-lookup"><span data-stu-id="c64ba-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="c64ba-175">Чтобы инициализировать приложение Blazor, когда документ готов, используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="c64ba-175">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="c64ba-176">Связывание с `Promise` в результате запуска вручную</span><span class="sxs-lookup"><span data-stu-id="c64ba-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="c64ba-177">Для выполнения дополнительных задач, таких как инициализация взаимодействия JS, используйте `then` для привязки к объекту `Promise`, полученному в результате запуска приложения Blazor вручную.</span><span class="sxs-lookup"><span data-stu-id="c64ba-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a><span data-ttu-id="c64ba-178">Настройка клиента SignalR</span><span class="sxs-lookup"><span data-stu-id="c64ba-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="c64ba-179">Ведение журнала</span><span class="sxs-lookup"><span data-stu-id="c64ba-179">Logging</span></span>

<span data-ttu-id="c64ba-180">Чтобы настроить ведение журнала клиента SignalR, передайте объект конфигурации (`configureSignalR`), который вызывает `configureLogging` с уровнем журнала для построителя клиента:</span><span class="sxs-lookup"><span data-stu-id="c64ba-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="c64ba-181">В предыдущем примере `information` эквивалентно уровню журнала <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="c64ba-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="c64ba-182">Изменение обработчика повторного подключения</span><span class="sxs-lookup"><span data-stu-id="c64ba-182">Modify the reconnection handler</span></span>

<span data-ttu-id="c64ba-183">Для событий подключения канала обработчика повторного подключения можно настроить пользовательское поведение, например следующим способом:</span><span class="sxs-lookup"><span data-stu-id="c64ba-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="c64ba-184">уведомлять пользователя, если подключение было разорвано;.</span><span class="sxs-lookup"><span data-stu-id="c64ba-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="c64ba-185">вносить записи в журнал (со стороны клиента) при подключении канала.</span><span class="sxs-lookup"><span data-stu-id="c64ba-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="c64ba-186">Чтобы изменить события соединения, зарегистрируйте обратные вызовы для следующих изменений соединения:</span><span class="sxs-lookup"><span data-stu-id="c64ba-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="c64ba-187">для прерванных соединений используется `onConnectionDown`;</span><span class="sxs-lookup"><span data-stu-id="c64ba-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="c64ba-188">для устанавливаемых и повторно устанавливаемых соединений используется `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="c64ba-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="c64ba-189">Необходимо задать **как** `onConnectionDown`, так и `onConnectionUp`:</span><span class="sxs-lookup"><span data-stu-id="c64ba-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="c64ba-190">Настройка числа попыток и интервала повторного подключения</span><span class="sxs-lookup"><span data-stu-id="c64ba-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="c64ba-191">Чтобы настроить число попыток и интервал повторного подключения, задайте число повторных попыток (`maxRetries`) и периодичность в миллисекундах для каждой повторной попытки (`retryIntervalMilliseconds`):</span><span class="sxs-lookup"><span data-stu-id="c64ba-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="c64ba-192">Скрытие или замена отображаемого элемента повторного подключения</span><span class="sxs-lookup"><span data-stu-id="c64ba-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="c64ba-193">Чтобы скрыть отображаемый элемент повторного подключения, задайте для свойства `_reconnectionDisplay` обработчика повторных подключений пустой объект (`{}` или `new Object()`):</span><span class="sxs-lookup"><span data-stu-id="c64ba-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="c64ba-194">Чтобы заменить отображаемый элемент повторного подключения, задайте для `_reconnectionDisplay` в предыдущем примере нужный элемент:</span><span class="sxs-lookup"><span data-stu-id="c64ba-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="c64ba-195">Заполнитель `{ELEMENT ID}` — это идентификатор элемента HTML для отображения.</span><span class="sxs-lookup"><span data-stu-id="c64ba-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c64ba-196">Чтобы настроить задержку перед появлением отображаемого элемента повторного подключения, задайте свойство `transition-delay` в файле CSS приложения (`wwwroot/css/site.css`) для модального элемента.</span><span class="sxs-lookup"><span data-stu-id="c64ba-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="c64ba-197">В следующем примере задержка перехода изменяется с 500 мс (по умолчанию) на 1000 мс (1 секунда):</span><span class="sxs-lookup"><span data-stu-id="c64ba-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="c64ba-198">Отключение канала Blazor от клиента</span><span class="sxs-lookup"><span data-stu-id="c64ba-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="c64ba-199">По умолчанию канал Blazor отключается при активации [события страницы `unload`](https://developer.mozilla.org/docs/Web/API/Window/unload_event).</span><span class="sxs-lookup"><span data-stu-id="c64ba-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="c64ba-200">Чтобы отключить канал от клиента в других сценариях, вызовите `Blazor.disconnect` в соответствующем обработчике событий.</span><span class="sxs-lookup"><span data-stu-id="c64ba-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="c64ba-201">В следующем примере канал отключается, когда скрывается страница ([событие `pagehide`](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="c64ba-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c64ba-202">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c64ba-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="c64ba-203">События повторного подключения и события жизненного цикла Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c64ba-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
