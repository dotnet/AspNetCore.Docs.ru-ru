---
title: Настройка параметров для веб-сервера Kestrel для ASP.NET Core
author: rick-anderson
description: Сведения о настройке параметров для Kestrel, кроссплатформенного веб-сервера для ASP.NET Core.
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253868"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="52a02-103">Настройка параметров для веб-сервера Kestrel для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52a02-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="52a02-104">Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.</span><span class="sxs-lookup"><span data-stu-id="52a02-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="52a02-105">Чтобы задать дополнительную конфигурацию после вызова `ConfigureWebHostDefaults`, используйте `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="52a02-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="52a02-106">Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="52a02-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="52a02-107">Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="52a02-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="52a02-108">В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.</span><span class="sxs-lookup"><span data-stu-id="52a02-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="52a02-109">В примерах, приведенных далее в этой статье, параметры Kestrel настраиваются в коде C#.</span><span class="sxs-lookup"><span data-stu-id="52a02-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="52a02-110">Параметры Kestrel можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="52a02-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="52a02-111">Например, [поставщик конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider) может загрузить конфигурацию Kestrel из файла *appsettings.json* или *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="52a02-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="52a02-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> и [конфигурацию конечных точек](xref:fundamentals/servers/kestrel/endpoints) можно настроить из поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="52a02-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="52a02-113">Оставшаяся конфигурация Kestrel должна настраиваться в коде C#.</span><span class="sxs-lookup"><span data-stu-id="52a02-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="52a02-114">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="52a02-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="52a02-115">Настройте Kestrel в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="52a02-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="52a02-116">Внедрение экземпляра `IConfiguration` в класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="52a02-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="52a02-117">В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="52a02-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="52a02-118">В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:</span><span class="sxs-lookup"><span data-stu-id="52a02-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="52a02-119">Настройте Kestrel при сборке узла:</span><span class="sxs-lookup"><span data-stu-id="52a02-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="52a02-120">В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.</span><span class="sxs-lookup"><span data-stu-id="52a02-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="52a02-121">Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="52a02-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="52a02-122">Общие ограничения</span><span class="sxs-lookup"><span data-stu-id="52a02-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="52a02-123">Время ожидания проверки на активность</span><span class="sxs-lookup"><span data-stu-id="52a02-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="52a02-124">Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="52a02-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="52a02-125">Значение по умолчанию — 2 минуты.</span><span class="sxs-lookup"><span data-stu-id="52a02-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="52a02-126">максимальное число клиентских подключений;</span><span class="sxs-lookup"><span data-stu-id="52a02-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="52a02-127">Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="52a02-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="52a02-128">Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket).</span><span class="sxs-lookup"><span data-stu-id="52a02-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="52a02-129">После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="52a02-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="52a02-130">По умолчанию максимальное число подключений не ограничено (null).</span><span class="sxs-lookup"><span data-stu-id="52a02-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="52a02-131">максимальный размер текста запроса;</span><span class="sxs-lookup"><span data-stu-id="52a02-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="52a02-132">По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.</span><span class="sxs-lookup"><span data-stu-id="52a02-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="52a02-133">Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:</span><span class="sxs-lookup"><span data-stu-id="52a02-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="52a02-134">Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:</span><span class="sxs-lookup"><span data-stu-id="52a02-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="52a02-135">Переопределите параметр для конкретного запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="52a02-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="52a02-136">Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение.</span><span class="sxs-lookup"><span data-stu-id="52a02-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="52a02-137">Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.</span><span class="sxs-lookup"><span data-stu-id="52a02-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="52a02-138">При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено.</span><span class="sxs-lookup"><span data-stu-id="52a02-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="52a02-139">Это связано с тем, что оно уже задается в IIS.</span><span class="sxs-lookup"><span data-stu-id="52a02-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="52a02-140">минимальная скорость передачи данных в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="52a02-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="52a02-141">Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду.</span><span class="sxs-lookup"><span data-stu-id="52a02-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="52a02-142">Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня.</span><span class="sxs-lookup"><span data-stu-id="52a02-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="52a02-143">В течение этого периода скорость не проверяется.</span><span class="sxs-lookup"><span data-stu-id="52a02-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="52a02-144">Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.</span><span class="sxs-lookup"><span data-stu-id="52a02-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="52a02-145">Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.</span><span class="sxs-lookup"><span data-stu-id="52a02-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="52a02-146">Минимальная скорость также применяется к отклику.</span><span class="sxs-lookup"><span data-stu-id="52a02-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="52a02-147">Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="52a02-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="52a02-148">Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="52a02-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="52a02-149">Переопределите ограничения минимальной скорости для каждого запроса в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="52a02-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="52a02-150"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, который упоминается в предыдущем примере, отсутствует в `HttpContext.Features` для запросов HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="52a02-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="52a02-151">Изменение ограничений скорости на уровне отдельных запросов обычно не поддерживается для HTTP/2, поскольку этот протокол поддерживает мультиплексирование запросов.</span><span class="sxs-lookup"><span data-stu-id="52a02-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="52a02-152">Но возможности <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> все еще присутствуют в `HttpContext.Features` для запросов HTTP/2, так как ограничение скорости чтения может быть *полностью отключено* для отдельных запросов. Чтобы сделать это, задайте для параметра `IHttpMinRequestBodyDataRateFeature.MinDataRate` значение `null` (даже для запроса HTTP/2).</span><span class="sxs-lookup"><span data-stu-id="52a02-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="52a02-153">При попытке чтения свойства `IHttpMinRequestBodyDataRateFeature.MinDataRate` или при попытке задать для него значение, отличное от `null`, возникнет исключение `NotSupportedException` для запроса HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="52a02-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="52a02-154">Ограничения скорости на уровне сервера, которые настроены с помощью `KestrelServerOptions.Limits`, по-прежнему применяются к подключениям HTTP/1.x и HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="52a02-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="52a02-155">Время ожидания для заголовков запросов</span><span class="sxs-lookup"><span data-stu-id="52a02-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="52a02-156">Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов.</span><span class="sxs-lookup"><span data-stu-id="52a02-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="52a02-157">Значение по умолчанию — 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="52a02-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="52a02-158">Ограничения HTTP/2</span><span class="sxs-lookup"><span data-stu-id="52a02-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="52a02-159">Максимальное число потоков на подключение</span><span class="sxs-lookup"><span data-stu-id="52a02-159">Maximum streams per connection</span></span>

<span data-ttu-id="52a02-160">`Http2.MaxStreamsPerConnection` ограничивает количество параллельных потоков запросов для одного соединения HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="52a02-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="52a02-161">Потоки сверх этого числа будут отклонены.</span><span class="sxs-lookup"><span data-stu-id="52a02-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="52a02-162">Значение по умолчанию — 100.</span><span class="sxs-lookup"><span data-stu-id="52a02-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="52a02-163">Размер таблицы заголовка</span><span class="sxs-lookup"><span data-stu-id="52a02-163">Header table size</span></span>

<span data-ttu-id="52a02-164">Декодер HPACK распаковывает заголовки HTTP для подключений HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="52a02-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="52a02-165">`Http2.HeaderTableSize` ограничивает размер таблицы сжатия заголовка, которую использует декодер HPACK.</span><span class="sxs-lookup"><span data-stu-id="52a02-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="52a02-166">Это значение указывается в октетах и должно быть больше нуля (0).</span><span class="sxs-lookup"><span data-stu-id="52a02-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="52a02-167">Значение по умолчанию — 4096.</span><span class="sxs-lookup"><span data-stu-id="52a02-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="52a02-168">Максимальный размер кадра</span><span class="sxs-lookup"><span data-stu-id="52a02-168">Maximum frame size</span></span>

<span data-ttu-id="52a02-169">`Http2.MaxFrameSize` указывает максимально допустимый размер полезных данных в кадре подключения HTTP/2, получаемых или отправляемых сервером.</span><span class="sxs-lookup"><span data-stu-id="52a02-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="52a02-170">Это значение указывается в октетах и должно находиться в пределах от 2^14 (16 384) до 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="52a02-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="52a02-171">Значение по умолчанию — 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="52a02-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="52a02-172">Максимальный размер запроса заголовка</span><span class="sxs-lookup"><span data-stu-id="52a02-172">Maximum request header size</span></span>

<span data-ttu-id="52a02-173">`Http2.MaxRequestHeaderFieldSize` указывает максимально допустимый размер значений заголовка запроса (в октетах).</span><span class="sxs-lookup"><span data-stu-id="52a02-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="52a02-174">Это ограничение применяется к имени и значению в их сжатых и несжатых представлениях.</span><span class="sxs-lookup"><span data-stu-id="52a02-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="52a02-175">Значение должно быть больше нуля.</span><span class="sxs-lookup"><span data-stu-id="52a02-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="52a02-176">Значение по умолчанию — 8 192.</span><span class="sxs-lookup"><span data-stu-id="52a02-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="52a02-177">Размер окна начального подключения</span><span class="sxs-lookup"><span data-stu-id="52a02-177">Initial connection window size</span></span>

<span data-ttu-id="52a02-178">`Http2.InitialConnectionWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для всех запросов (потоков) на каждое соединение.</span><span class="sxs-lookup"><span data-stu-id="52a02-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="52a02-179">Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="52a02-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="52a02-180">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="52a02-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="52a02-181">Значение по умолчанию — 128 КБ (131 072).</span><span class="sxs-lookup"><span data-stu-id="52a02-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="52a02-182">Размер окна начального потока</span><span class="sxs-lookup"><span data-stu-id="52a02-182">Initial stream window size</span></span>

<span data-ttu-id="52a02-183">`Http2.InitialStreamWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для каждого запроса (потока).</span><span class="sxs-lookup"><span data-stu-id="52a02-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="52a02-184">Размеры запросов также ограничиваются параметром `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="52a02-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="52a02-185">Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="52a02-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="52a02-186">Значение по умолчанию — 96 КБ (98 304).</span><span class="sxs-lookup"><span data-stu-id="52a02-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="52a02-187">Конфигурация проверки связи для проверки активности HTTP/2</span><span class="sxs-lookup"><span data-stu-id="52a02-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="52a02-188">Kestrel можно настроить для отправки пакетов проверки связи HTTP/2 подключенным клиентам.</span><span class="sxs-lookup"><span data-stu-id="52a02-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="52a02-189">Проверка связи HTTP/2 служит нескольким целям.</span><span class="sxs-lookup"><span data-stu-id="52a02-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="52a02-190">Сохранение активности бездействующего подключения.</span><span class="sxs-lookup"><span data-stu-id="52a02-190">Keep idle connections alive.</span></span> <span data-ttu-id="52a02-191">Некоторые клиенты и прокси-серверы закрывают неактивные соединения.</span><span class="sxs-lookup"><span data-stu-id="52a02-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="52a02-192">Проверки связи HTTP/2 рассматриваются как активность подключения и препятствуют закрытию соединения как бездействующего.</span><span class="sxs-lookup"><span data-stu-id="52a02-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="52a02-193">Закрытие неработоспособных подключений.</span><span class="sxs-lookup"><span data-stu-id="52a02-193">Close unhealthy connections.</span></span> <span data-ttu-id="52a02-194">Подключения, в которых клиент не отвечает на проверку связи для проверки активности в течение заданного времени, закрываются сервером.</span><span class="sxs-lookup"><span data-stu-id="52a02-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="52a02-195">Для настройки проверки связи для проверки активности HTTP/2 есть два параметра.</span><span class="sxs-lookup"><span data-stu-id="52a02-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="52a02-196">`Http2.KeepAlivePingInterval` — это `TimeSpan`, который настраивает интервал проверки связи.</span><span class="sxs-lookup"><span data-stu-id="52a02-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="52a02-197">Если сервер не получает никаких кадров данных в течение этого периода времени, он отправляет клиенту пакеты проверки связи для проверки активности.</span><span class="sxs-lookup"><span data-stu-id="52a02-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="52a02-198">Если для этого параметра задано значение `TimeSpan.MaxValue`, то проверка связи для проверки активности отключена.</span><span class="sxs-lookup"><span data-stu-id="52a02-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="52a02-199">Значение по умолчанию — `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="52a02-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="52a02-200">`Http2.KeepAlivePingTimeout` — это `TimeSpan`, который настраивает время ожидания проверки связи.</span><span class="sxs-lookup"><span data-stu-id="52a02-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="52a02-201">Если сервер не получает никаких кадров данных, например ответ на запрос проверки связи, подключение закрывается.</span><span class="sxs-lookup"><span data-stu-id="52a02-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="52a02-202">Если для этого параметра задано значение `TimeSpan.MaxValue`, то время ожидания проверки активности отключено.</span><span class="sxs-lookup"><span data-stu-id="52a02-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="52a02-203">Значение по умолчанию — 20 секунд.</span><span class="sxs-lookup"><span data-stu-id="52a02-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="52a02-204">Другие варианты</span><span class="sxs-lookup"><span data-stu-id="52a02-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="52a02-205">Синхронный ввод-вывод</span><span class="sxs-lookup"><span data-stu-id="52a02-205">Synchronous I/O</span></span>

<span data-ttu-id="52a02-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа.</span><span class="sxs-lookup"><span data-stu-id="52a02-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="52a02-207">Значение по умолчанию — `false`.</span><span class="sxs-lookup"><span data-stu-id="52a02-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="52a02-208">Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения.</span><span class="sxs-lookup"><span data-stu-id="52a02-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="52a02-209">Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.</span><span class="sxs-lookup"><span data-stu-id="52a02-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="52a02-210">В примере ниже включаются синхронные операции ввода-вывода:</span><span class="sxs-lookup"><span data-stu-id="52a02-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="52a02-211">Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="52a02-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
