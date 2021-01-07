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
# <a name="aspnet-core-no-locsignalr-configuration"></a>Конфигурация SignalR ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` . `AddJsonProtocol`Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).

Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:

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
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Newtonsoft.Js

Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки SignalR концентраторов.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |
| `StreamBufferCapacity` | `10` | Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента. При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |
| `MaximumParallelInvocationsPerClient` | 1 | Максимальное число методов концентратора, которые каждый клиент может вызывать параллельно перед постановкой в очередь. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .

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

Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .

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

В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки. Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой. Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |
| `MinimumProtocolVersion` | 0 | Укажите минимальную версию протокола Negotiate. Используется для ограничения клиентов на более новые версии. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала. Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни ведения журнала. Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал. Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.

| Строка                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**или**`information` | `LogLevel.Information` |
| `warn`**или**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).

SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` . Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRКлиент Java пока не поддерживает откат транспорта.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :

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

В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Empty | Коллекция HTTP cookie s для отправки с каждым HTTP-запросом. |
| `Credentials` | Empty | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения. |
| `headers` | `null` | Словарь заголовков, отправленных с каждым HTTP-запросом. Отправка заголовков в браузере не работает для WebSockets или <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> потока. |
| `logMessageContent` | `null` | Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом. |
| `skipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `withCredentials` | `true` | Указывает, будут ли учетные данные отправляться с запросом CORS. Служба приложений Azure использует cookie для прикрепленных сеансов s и требует, чтобы этот параметр был включен правильно. Дополнительные сведения о CORS с SignalR см. в разделе <xref:signalr/security#cross-origin-resource-sharing> . |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `withHeader` `withHeaders` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :

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

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` . `AddJsonProtocol`Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).

Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:

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
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Newtonsoft.Js

Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки SignalR концентраторов.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |
| `StreamBufferCapacity` | `10` | Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента. При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .

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

Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .

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

В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки. Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой. Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |
| `MinimumProtocolVersion` | 0 | Укажите минимальную версию протокола Negotiate. Используется для ограничения клиентов на более новые версии. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала. Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни ведения журнала. Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал. Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.

| Строка                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**или**`information` | `LogLevel.Information` |
| `warn`**или**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).

SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` . Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRКлиент Java пока не поддерживает откат транспорта.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :

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

В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Empty | Коллекция HTTP cookie s для отправки с каждым HTTP-запросом. |
| `Credentials` | Empty | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения. |
| `logMessageContent` | `null` | Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом. |
| `skipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `withHeader` `withHeaders` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` . `AddJsonProtocol`Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализероптионс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) этого объекта — это `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> объект, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [System.Text.Jsдокументации](/dotnet/api/system.text.json).

Например, чтобы настроить в сериализаторе не изменять регистр имен свойств, вместо имен по умолчанию используется следующий код `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:

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
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Переключиться на Newtonsoft.Js

Если вам нужны функции `Newtonsoft.Json` , которые не поддерживаются в `System.Text.Json` , см. раздел [Переключение на Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки SignalR концентраторов.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |
| `StreamBufferCapacity` | `10` | Максимальное число элементов, которые могут быть помещены в буфер для потоков загрузки клиента. При достижении этого предела обработка вызовов блокируется до тех пор, пока сервер не обработает потоковые элементы.|
| `MaximumReceiveMessageSize` | 32 КБ | Максимальный размер одного входящего сообщения концентратора. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .

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

Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) в `Startup.Configure` .

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

В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который буфер сервера перед применением обратной нагрузки. Увеличение этого значения позволяет серверу быстрее принимать сообщения большего размера без применения недостаточной нагрузки, но может увеличить потребление памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые были буфером сервера, прежде чем наблюдать за нехваткой. Увеличение этого значения позволяет серверу быстрее занимать больше сообщений, не ожидая перехода на резервную нагрузку, но может увеличить потребление памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Вместо `LogLevel` значения можно также указать `string` значение, представляющее имя уровня журнала. Это полезно при настройке SignalR ведения журнала в средах, где у вас нет доступа к `LogLevel` константам.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

В следующей таблице перечислены доступные уровни ведения журнала. Значение, которое вы `configureLogging` указываете для установки **минимального** уровня ведения журнала, который будет записан в журнал. Сообщения, зарегистрированные на этом уровне **или перечисленные в таблице уровни**, будут занесены в журнал.

| Строка                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**или**`information` | `LogLevel.Information` |
| `warn`**или**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).

SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

В клиенте Java транспорт выбирается с помощью `withTransport` метода в `HttpHubConnectionBuilder` . Клиент Java по умолчанию использует транспорт WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalRКлиент Java пока не поддерживает откат транспорта.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :

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

В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Empty | Коллекция HTTP cookie s для отправки с каждым HTTP-запросом. |
| `Credentials` | Empty | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения. |
| `logMessageContent` | `null` | Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом. |
| `skipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `withHeader` `withHeaders` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод. `AddJsonProtocol`Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:

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
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки SignalR концентраторов.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 секунд | Сервер будет считать, что Клиент отключен, если в этот интервал не было получено сообщение (включая "срок поддержания активности"). Чтобы клиент фактически был помечен как отключенный, он может занять больше времени, чем это время ожидания, из-за того, как это реализовано. Рекомендуемое значение — двойное `KeepAliveInterval` значение.|
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .

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

Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .

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

В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который является буфером сервера. Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые используются в буфере сервера. Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).

SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

В этой версии клиентские WebSocket-сокеты Java являются единственным доступным транспортом.

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :

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

В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `keepAliveIntervalInMilliseconds` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 секунд (15 000 миллисекунд) | Определяет интервал, с которым клиент отправляет сообщения проверки связи. Отправка любого сообщения от клиента сбрасывает таймер до начала интервала. Если клиент не отправил сообщение в `ClientTimeoutInterval` наборе на сервере, сервер считает, что Клиент отключен. |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Empty | Коллекция HTTP cookie s для отправки с каждым HTTP-запросом. |
| `Credentials` | Empty | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения. |
| `logMessageContent` | `null` | Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом. |
| `skipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `withHeader` `withHeaders` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Параметры сериализации JSON/MessagePack

ASP.NET Core SignalR поддерживает два протокола кодирования сообщений: [JSON](https://www.json.org/) и [MessagePack](https://msgpack.org/index.html). Для каждого протокола предусмотрены параметры конфигурации сериализации.

Сериализацию JSON можно настроить на сервере с помощью метода расширения [адджсонпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , который можно добавить после [добавления SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) в `Startup.ConfigureServices` метод. `AddJsonProtocol`Метод принимает делегат, который получает `options` объект. Свойство [пайлоадсериализерсеттингс](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) этого объекта является `JsonSerializerSettings` объектом JSON.NET, который можно использовать для настройки сериализации аргументов и возвращаемых значений. Дополнительные сведения см. в [документации по JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Например, чтобы настроить сериализатор для использования имен свойств "PascalCase" вместо имен по умолчанию "camelCase", используйте следующий код в `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

В клиенте .NET такой же `AddJsonProtocol` метод расширения существует в [хубконнектионбуилдер](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Чтобы разрешить метод расширения, необходимо импортировать пространство имен:

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
> В настоящее время невозможно настроить сериализацию JSON в клиенте JavaScript.

### <a name="messagepack-serialization-options"></a>Параметры сериализации MessagePack

MessagePack сериализацию можно настроить, предоставив делегат вызову [аддмессажепаккпротокол](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Дополнительные сведения см. [в разделе MessagePack в SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> В настоящее время невозможно настроить сериализацию MessagePack в клиенте JavaScript.

## <a name="configure-server-options"></a>Настройка параметров сервера

В следующей таблице описаны параметры для настройки SignalR концентраторов.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 секунд | Если клиент не отправляет исходное сообщение подтверждения в течение этого интервала времени, соединение закрывается. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 секунд | Если сервер не отправил сообщение в течение этого интервала, сообщение проверки связи отправляется автоматически, чтобы подключение не открывалось. При изменении `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` параметра на клиенте измените параметр. Рекомендуемое `ServerTimeout` / `serverTimeoutInMilliseconds` значение — двойное `KeepAliveInterval` значение.  |
| `SupportedProtocols` | Все установленные протоколы | Протоколы, поддерживаемые этим концентратором. По умолчанию все зарегистрированные на сервере протоколы разрешены, но в этом списке можно удалить протоколы, чтобы отключить определенные протоколы для отдельных концентраторов. |
| `EnableDetailedErrors` | `false` | Если значение `true` равно, подробные сообщения об исключениях возвращаются клиентам при возникновении исключения в методе концентратора. Значение по умолчанию — `false` , так как эти сообщения об исключениях могут содержать конфиденциальные сведения. |

Параметры можно настроить для всех концентраторов, предоставив делегаты параметров для `AddSignalR` вызова в `Startup.ConfigureServices` .

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

Параметры одного концентратора переопределяют глобальные параметры, предоставленные в `AddSignalR` , и могут быть настроены с помощью <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Дополнительные параметры конфигурации HTTP

Используется `HttpConnectionDispatcherOptions` для настройки дополнительных параметров, относящихся к транспортам и управлению буферами памяти. Эти параметры настраиваются путем передачи делегата в [мафуб \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) в `Startup.Configure` .

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

В следующей таблице описаны параметры для настройки SignalR расширенных HTTP-параметров ASP.NET Core.

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 КБ | Максимальное число байтов, полученных от клиента, который является буфером сервера. Увеличение этого значения позволяет серверу принимать сообщения большего размера, однако это может негативно сказаться на потреблении памяти. |
| `AuthorizationData` | Данные автоматически собираются из `Authorize` атрибутов, применяемых к классу Hub. | Список объектов [иаусоризедата](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , которые позволяют определить, разрешено ли клиенту подключаться к концентратору. |
| `TransportMaxBufferSize` | 32 КБ | Максимальное число байтов, отправленных приложением, которые используются в буфере сервера. Увеличение этого значения позволяет серверу передавать большие сообщения, но может негативно сказаться на потреблении памяти. |
| `Transports` | Все транспорты включены. | Битовое перечисление `HttpTransportType` значений, которое может ограничивать возможности транспорта, которые клиент может использовать для подключения. |
| `LongPolling` | См. ниже. | Дополнительные параметры, относящиеся к длительному транспортному потоку. |
| `WebSockets` | См. ниже. | Дополнительные параметры, относящиеся к транспортному протоколу WebSocket. |

У транспорта с длинным опросом есть дополнительные параметры, которые можно настроить с помощью `LongPolling` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 секунд | Максимальное количество времени, в течение которого сервер ожидает отправки сообщения клиенту перед завершением одного запроса опроса. Уменьшение этого значения приводит к тому, что клиент будет чаще выдавать новые запросы на опрос. |

Транспорт WebSocket имеет дополнительные параметры, которые можно настроить с помощью `WebSockets` Свойства:

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 с | Когда сервер закрывается, если не удается закрыть клиент в течение этого интервала времени, соединение будет разорвано. |
| `SubProtocolSelector` | `null` | Делегат, который можно использовать для задания `Sec-WebSocket-Protocol` заголовка в пользовательском значении. Делегат получает значения, запрошенные клиентом в качестве входных данных, и ожидается, что он возвращает нужное значение. |

## <a name="configure-client-options"></a>Настройка параметров клиента

Параметры клиента можно настроить для `HubConnectionBuilder` типа (доступного в клиентах .NET и JavaScript). Он также доступен в клиенте Java, но он `HttpHubConnectionBuilder` содержит параметры конфигурации построителя, а также сам подкласс `HubConnection` .

### <a name="configure-logging"></a>Настройка журнала

Ведение журнала настраивается в клиенте .NET с помощью `ConfigureLogging` метода. Регистраторы и фильтры могут быть зарегистрированы так же, как и на сервере. Дополнительные сведения см. в документации по [ведению журнала ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Чтобы зарегистрировать регистраторы, необходимо установить необходимые пакеты. Полный список см. в разделе [встроенные поставщики ведения журналов](xref:fundamentals/logging/index#built-in-logging-providers) документации.

Например, чтобы включить ведение журнала консоли, установите `Microsoft.Extensions.Logging.Console` пакет NuGet. Вызовите `AddConsole` метод расширения:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

В клиенте JavaScript аналогичный `configureLogging` метод существует. Укажите `LogLevel` значение, указывающее минимальный уровень создаваемых сообщений журнала. Журналы записываются в окно консоли браузера.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Чтобы полностью отключить ведение журнала, укажите `signalR.LogLevel.None` в `configureLogging` методе.

Дополнительные сведения о ведении журналов см. в [ SignalR документации по диагностике](xref:signalr/diagnostics).

SignalRКлиент Java использует библиотеку [SLF4J](https://www.slf4j.org/) для ведения журнала. Это высокоуровневый API ведения журнала, который позволяет пользователям библиотеки выбирать собственную реализацию ведения журнала, применяя определенную зависимость от ведения журнала. В следующем фрагменте кода показано, как использовать `java.util.logging` с SignalR клиентом Java.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Если вы не настраиваете ведение журнала в зависимостях, SLF4J загружает средство ведения журнала без операций по умолчанию со следующим предупреждающим сообщением:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Это можно игнорировать.

### <a name="configure-allowed-transports"></a>Настройка разрешенных транспортов

Транспорты, используемые, SignalR можно настроить в `WithUrl` вызове ( `withUrl` в JavaScript). Побитовое или для значений `HttpTransportType` можно использовать, чтобы ограничить клиент использованием только указанных транспортов. По умолчанию все транспорты включены.

Например, чтобы отключить транспорт Server-Sent событий, но разрешить соединения WebSockets и long опрашиваете:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

В клиенте JavaScript транспорты настраиваются путем установки поля для `transport` объекта Options, указанного в `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Настройка проверки подлинности носителя

Чтобы предоставить данные проверки подлинности вместе с SignalR запросами, используйте `AccessTokenProvider` параметр ( `accessTokenFactory` в JavaScript), чтобы указать функцию, которая возвращает нужный маркер доступа. В клиенте .NET этот маркер доступа передается в качестве токена "Проверка подлинности носителя" HTTP (с использованием `Authorization` заголовка с типом `Bearer` ). В клиенте JavaScript маркер доступа используется в качестве токена носителя, **за исключением** случаев, когда интерфейсы API браузера ограничивают возможность применения заголовков (в частности, в Server-Sent событиях и запросах WebSocket). В этих случаях маркер доступа предоставляется как значение строки запроса `access_token` .

В клиенте .NET `AccessTokenProvider` параметр можно указать с помощью делегата Options в `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

В клиенте JavaScript маркер доступа настраивается путем установки `accessTokenFactory` поля для объекта Options в `withUrl` :

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

В SignalR клиенте Java можно настроить токен носителя для использования при проверке подлинности, предоставив фабрику маркеров доступа для [хттфубконнектионбуилдер](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Используйте [висакцесстокенфактори](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) для предоставления [рксжава](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html). При вызове [Single. отсрочки](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)можно написать логику для создания маркеров доступа для клиента.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Настройка времени ожидания и параметров поддержания активности

Дополнительные параметры для настройки времени ожидания и проверки активности доступны для `HubConnection` самого объекта:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `Closed` событие ( `onclose` в JavaScript). Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |
| `HandshakeTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `Closed` событие ( `onclose` в JavaScript). Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

В клиенте .NET значения времени ожидания задаются в виде `TimeSpan` значений.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onclose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное значение сервера, `KeepAliveInterval` чтобы разрешить поступление проверки связи. |

# <a name="java"></a>[Java](#tab/java)

| Параметр | Значение по умолчанию | Описание |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 секунд (30 000 миллисекунд) | Время ожидания активности сервера. Если сервер не отправил сообщение в этот интервал, клиент считает, что сервер отключен, и активирует `onClose` событие. Это значение должно быть достаточно большим для отправки сообщения проверки связи с сервера **и** получения клиентом в течение интервала времени ожидания. Рекомендуемое значение — это число, по крайней мере, двойное `KeepAliveInterval` значение сервера, которое позволяет получить время для проверки связи. |
| `withHandshakeResponseTimeout` | 15 секунд | Время ожидания для первоначального подтверждения сервера. Если сервер не отправляет ответ подтверждения в течение этого интервала, клиент отменяет подтверждение и активирует `onClose` событие. Это дополнительный параметр, который следует изменять, только если возникают ошибки времени ожидания подтверждения из-за серьезной задержки сети. Дополнительные сведения о процессе подтверждения связи см. в статье [ SignalR Спецификация протокола концентратора](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Настройка дополнительных параметров

Дополнительные параметры можно настроить в `WithUrl` `withUrl` методе (в JavaScript) для `HubConnectionBuilder` или в различных API-интерфейсах конфигурации в `HttpHubConnectionBuilder` клиенте Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Параметр .NET |  Значение по умолчанию | Описание |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `SkipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `ClientCertificates` | Empty | Коллекция сертификатов TLS для отправки в запросы проверки подлинности. |
| `Cookies` | Empty | Коллекция HTTP cookie s для отправки с каждым HTTP-запросом. |
| `Credentials` | Empty | Учетные данные для отправки с каждым HTTP-запросом. |
| `CloseTimeout` | 5 с | Только WebSockets. Максимальное время ожидания клиента после закрытия сервера для подтверждения запроса на закрытие. Если сервер не подтверждает закрытие в течение этого времени, клиент отключается. |
| `Headers` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |
| `HttpMessageHandlerFactory` | `null` | Делегат, который можно использовать для настройки или замены `HttpMessageHandler` используемого для отправки HTTP-запросов. Не используется для соединений WebSocket. Этот делегат должен возвращать значение, отличное от NULL, и получает значение по умолчанию в качестве параметра. Либо измените параметры для этого значения по умолчанию и верните его, либо возвратите новый `HttpMessageHandler` экземпляр. **При замене обработчика обязательно скопируйте параметры, которые нужно сохранить из указанного обработчика. в противном случае настроенные параметры (такие как Cookie s и headers) не будут применяться к новому обработчику.** |
| `Proxy` | `null` | HTTP-прокси, используемый при отправке HTTP-запросов. |
| `UseDefaultCredentials` | `false` | Установите это логическое значение, чтобы отправить учетные данные по умолчанию для запросов HTTP и WebSockets. Это позволяет использовать проверку подлинности Windows. |
| `WebSocketConfiguration` | `null` | Делегат, который можно использовать для настройки дополнительных параметров WebSocket. Получает экземпляр [клиентвебсоккетоптионс](/dotnet/api/system.net.websockets.clientwebsocketoptions) , который можно использовать для настройки параметров. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Параметр JavaScript | Значение по умолчанию | Описание |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Значение, указывающее транспорт, используемый для соединения. |
| `logMessageContent` | `null` | Задайте значение `true` , чтобы записывать в журнал байты и символы сообщений, отправленных и полученных клиентом. |
| `skipNegotiation` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |

# <a name="java"></a>[Java](#tab/java)

| Параметр Java | Значение по умолчанию | Описание |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Функция, возвращающая строку, которая предоставляется в качестве маркера проверки подлинности носителя в HTTP-запросах. |
| `shouldSkipNegotiate` | `false` | Установите этот параметр в значение `true` , чтобы пропустить шаг согласования. **Поддерживается только в том случае, если транспорт WebSocket является единственным включенным транспортом**. Этот параметр нельзя включить при использовании SignalR службы Azure. |
| `withHeader` `withHeaders` | Empty | Таблица дополнительных заголовков HTTP для отправки с каждым HTTP-запросом. |

---

В клиенте .NET эти параметры можно изменить с помощью делегата Options, предоставленного для `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

В клиенте JavaScript эти параметры можно предоставить в объекте JavaScript, предоставленном для `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

В клиенте Java эти параметры можно настроить с помощью методов, `HttpHubConnectionBuilder` возвращаемых из `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
