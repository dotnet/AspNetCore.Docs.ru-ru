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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>Настройка параметров для веб-сервера Kestrel для ASP.NET Core

Веб-сервер Kestrel имеет ограничительные параметры конфигурации, которые удобно использовать в развертываниях с выходом в Интернет.

Чтобы задать дополнительную конфигурацию после вызова `ConfigureWebHostDefaults`, используйте `ConfigureKestrel`:

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

Задать ограничения для свойства <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> в классе <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. Свойство `Limits` содержит экземпляр класса <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

В следующих примерах используется пространство имен <xref:Microsoft.AspNetCore.Server.Kestrel.Core>.

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

В примерах, приведенных далее в этой статье, параметры Kestrel настраиваются в коде C#. Параметры Kestrel можно также задать с помощью [поставщика конфигурации](xref:fundamentals/configuration/index). Например, [поставщик конфигурации файла](xref:fundamentals/configuration/index#file-configuration-provider) может загрузить конфигурацию Kestrel из файла *appsettings.json* или *appsettings.{Environment}.json*:

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> и [конфигурацию конечных точек](xref:fundamentals/servers/kestrel/endpoints) можно настроить из поставщиков конфигурации. Оставшаяся конфигурация Kestrel должна настраиваться в коде C#.

Воспользуйтесь **одним** из перечисленных ниже подходов.

* Настройте Kestrel в `Startup.ConfigureServices`:

  1. Внедрение экземпляра `IConfiguration` в класс `Startup`. В следующем примере предполагается, что введенная конфигурация назначается свойству `Configuration`.
  2. В `Startup.ConfigureServices` загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel:

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

* Настройте Kestrel при сборке узла:

  В *Program.cs* загрузите раздел конфигурации `Kestrel` в конфигурацию Kestrel.

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

Оба предыдущих подхода работают с любым [поставщиком конфигурации](xref:fundamentals/configuration/index).

## <a name="general-limits"></a>Общие ограничения

### <a name="keep-alive-timeout"></a>Время ожидания проверки на активность

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Получает или задает [время ожидания проверки на активность](https://tools.ietf.org/html/rfc7230#section-6.5). Значение по умолчанию — 2 минуты.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>максимальное число клиентских подключений;

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Максимальное число одновременно открытых подключений TCP для всего приложения можно задать с помощью следующего кода:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Существует отдельный предел по подключениям, измененным с HTTP или HTTPS на другой протокол (например, по запросу WebSocket). После изменения подключение не учитывается в пределе `MaxConcurrentConnections`.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

По умолчанию максимальное число подключений не ограничено (null).

### <a name="maximum-request-body-size"></a>максимальный размер текста запроса;

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

По умолчанию максимальный размер текста запроса составляет 30 000 000 байт, что примерно соответствует 28,6 МБ.

Чтобы переопределить это ограничение в приложении MVC ASP.NET Core, мы рекомендуем использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Приведенный ниже пример показывает, как настроить ограничение для приложения и каждого запроса:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Переопределите параметр для конкретного запроса в ПО промежуточного слоя:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Если приложение пытается настроить ограничение для запроса после того, как оно начало считывать запрос, возникает исключение. Доступно свойство `IsReadOnly`, указывающее, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.

При запуске приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), который обслуживает [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module), ограничение на размер текста запроса Kestrel будет отключено. Это связано с тем, что оно уже задается в IIS.

### <a name="minimum-request-body-data-rate"></a>минимальная скорость передачи данных в тексте запроса.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel каждую секунду проверяет, поступают ли данные с указанной скоростью в байтах в секунду. Если скорость падает ниже минимума, для соединения истекает время ожидания. Льготный период — это количество времени, которое Kestrel дает клиенту на увеличение его скорости отправки до минимального уровня. В течение этого периода скорость не проверяется. Льготный период помогает избежать разрыва соединений, которые первоначально отправляют данные с небольшой скоростью из-за медленного запуска TCP.

Минимальная скорость по умолчанию составляет 240 байт/с, льготный период равен 5 секундам.

Минимальная скорость также применяется к отклику. Код для задания лимита запросов и лимита откликов различается только наличием `RequestBody` или `Response` в именах свойств и интерфейсов.

Ниже приведен пример, показывающий, как настроить минимальную скорость передачи данных в *Program.cs*:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Переопределите ограничения минимальной скорости для каждого запроса в ПО промежуточного слоя:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, который упоминается в предыдущем примере, отсутствует в `HttpContext.Features` для запросов HTTP/2. Изменение ограничений скорости на уровне отдельных запросов обычно не поддерживается для HTTP/2, поскольку этот протокол поддерживает мультиплексирование запросов. Но возможности <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> все еще присутствуют в `HttpContext.Features` для запросов HTTP/2, так как ограничение скорости чтения может быть *полностью отключено* для отдельных запросов. Чтобы сделать это, задайте для параметра `IHttpMinRequestBodyDataRateFeature.MinDataRate` значение `null` (даже для запроса HTTP/2). При попытке чтения свойства `IHttpMinRequestBodyDataRateFeature.MinDataRate` или при попытке задать для него значение, отличное от `null`, возникнет исключение `NotSupportedException` для запроса HTTP/2.

Ограничения скорости на уровне сервера, которые настроены с помощью `KestrelServerOptions.Limits`, по-прежнему применяются к подключениям HTTP/1.x и HTTP/2.

### <a name="request-headers-timeout"></a>Время ожидания для заголовков запросов

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Получает или задает максимальное время, которое сервер уделяет получению заголовков запросов. Значение по умолчанию — 30 секунд.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>Ограничения HTTP/2

### <a name="maximum-streams-per-connection"></a>Максимальное число потоков на подключение

`Http2.MaxStreamsPerConnection` ограничивает количество параллельных потоков запросов для одного соединения HTTP/2. Потоки сверх этого числа будут отклонены.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Значение по умолчанию — 100.

### <a name="header-table-size"></a>Размер таблицы заголовка

Декодер HPACK распаковывает заголовки HTTP для подключений HTTP/2. `Http2.HeaderTableSize` ограничивает размер таблицы сжатия заголовка, которую использует декодер HPACK. Это значение указывается в октетах и должно быть больше нуля (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Значение по умолчанию — 4096.

### <a name="maximum-frame-size"></a>Максимальный размер кадра

`Http2.MaxFrameSize` указывает максимально допустимый размер полезных данных в кадре подключения HTTP/2, получаемых или отправляемых сервером. Это значение указывается в октетах и должно находиться в пределах от 2^14 (16 384) до 2^24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Значение по умолчанию — 2^14 (16 384).

### <a name="maximum-request-header-size"></a>Максимальный размер запроса заголовка

`Http2.MaxRequestHeaderFieldSize` указывает максимально допустимый размер значений заголовка запроса (в октетах). Это ограничение применяется к имени и значению в их сжатых и несжатых представлениях. Значение должно быть больше нуля.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Значение по умолчанию — 8 192.

### <a name="initial-connection-window-size"></a>Размер окна начального подключения

`Http2.InitialConnectionWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для всех запросов (потоков) на каждое соединение. Размеры запросов также ограничиваются параметром `Http2.InitialStreamWindowSize`. Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Значение по умолчанию — 128 КБ (131 072).

### <a name="initial-stream-window-size"></a>Размер окна начального потока

`Http2.InitialStreamWindowSize` указывает максимальное значение данных тела запроса (в байтах), буферизируемое сервером за один раз, для каждого запроса (потока). Размеры запросов также ограничиваются параметром `Http2.InitialConnectionWindowSize`. Значение должно быть больше или равно 65 535 и меньше 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Значение по умолчанию — 96 КБ (98 304).

### <a name="http2-keep-alive-ping-configuration"></a>Конфигурация проверки связи для проверки активности HTTP/2

Kestrel можно настроить для отправки пакетов проверки связи HTTP/2 подключенным клиентам. Проверка связи HTTP/2 служит нескольким целям.

* Сохранение активности бездействующего подключения. Некоторые клиенты и прокси-серверы закрывают неактивные соединения. Проверки связи HTTP/2 рассматриваются как активность подключения и препятствуют закрытию соединения как бездействующего.
* Закрытие неработоспособных подключений. Подключения, в которых клиент не отвечает на проверку связи для проверки активности в течение заданного времени, закрываются сервером.

Для настройки проверки связи для проверки активности HTTP/2 есть два параметра.

* `Http2.KeepAlivePingInterval` — это `TimeSpan`, который настраивает интервал проверки связи. Если сервер не получает никаких кадров данных в течение этого периода времени, он отправляет клиенту пакеты проверки связи для проверки активности. Если для этого параметра задано значение `TimeSpan.MaxValue`, то проверка связи для проверки активности отключена. Значение по умолчанию — `TimeSpan.MaxValue`.
* `Http2.KeepAlivePingTimeout` — это `TimeSpan`, который настраивает время ожидания проверки связи. Если сервер не получает никаких кадров данных, например ответ на запрос проверки связи, подключение закрывается. Если для этого параметра задано значение `TimeSpan.MaxValue`, то время ожидания проверки активности отключено. Значение по умолчанию — 20 секунд.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>Другие варианты

### <a name="synchronous-io"></a>Синхронный ввод-вывод

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> определяет, разрешены ли синхронные операции ввода-вывода для запроса и ответа. Значение по умолчанию — `false`.

> [!WARNING]
> Выполнение большого числа заблокированных операций синхронного ввода-вывода может привести к перегрузке пула потоков и зависанию приложения. Включайте `AllowSynchronousIO`, только если вы используете библиотеку, которая не поддерживает асинхронные операции ввода-вывода.

В примере ниже включаются синхронные операции ввода-вывода:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Сведения о других параметрах и ограничениях Kestrel см. в следующих разделах:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
