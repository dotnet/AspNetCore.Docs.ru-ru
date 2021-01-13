---
title: Вызов служб gRPC с помощью клиента .NET
author: jamesnk
description: Узнайте, как вызывать службы gRPC с помощью клиента gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/18/2020
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
uid: grpc/client
ms.openlocfilehash: 39f9b3fde19e31ca970668552e5829308705f513
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97699138"
---
# <a name="call-grpc-services-with-the-net-client"></a>Вызов служб gRPC с помощью клиента .NET

Клиентская библиотека .NET gRPC доступна в пакете NuGet [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client). В этом документе объясняется, как выполнять следующие задачи:

* Настройка клиента gRPC для вызова служб gRPC.
* Вызовы gRPC для унарного метода, методов потоковой передачи сервера, потоковой передачи клиента и двунаправленной потоковой передачи.

## <a name="configure-grpc-client"></a>Настройка клиента gRPC

Клиенты gRPC являются конкретными типами клиентов, [создаваемыми в файлах *\*.proto*](xref:grpc/basics#generated-c-assets). Конкретный клиент gRPC использует методы, которые выполняют преобразование для служб gRPC в файле *\*.proto*. Например, служба с именем `Greeter` создает тип `GreeterClient` с методами для вызова службы.

Клиент gRPC создается из канала. Для начала воспользуйтесь `GrpcChannel.ForAddress`, чтобы создать канал, а затем используйте канал для создания клиента gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Канал представляет собой долгосрочное подключение к службе gRPC. Создаваемый канал настраивается с параметрами с учетом вызова службы. Например, `HttpClient`, используемый для выполнения вызовов, максимальный размер сообщения для отправки и получения, а также ведение журнала можно указать в `GrpcChannelOptions` и использовать с `GrpcChannel.ForAddress`. Полный список параметров см. в разделе, [посвященном параметрам конфигурации клиента](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>Настройка TLS

Клиент gRPC должен использовать ту же систему безопасности на уровне подключения, что и вызванная служба. Протокол TLS для клиента gRPC настраивается при создании канала gRPC. Если система безопасности на уровне подключения канала и службы не совпадает, при вызове службы от клиента gRPC поступает сообщение об ошибке.

Чтобы настроить канал gRPC для использования протокола TLS, убедитесь, что адрес сервера начинается с `https`. Например, `GrpcChannel.ForAddress("https://localhost:5001")` использует протокол HTTPS. Канал gRPC автоматически согласует подключение, защищенное с помощью TLS, и использует безопасное соединение для выполнения вызовов gRPC.

> [!TIP]
> gRPC поддерживает проверку подлинности на основе сертификата клиента по протоколу TLS. Сведения о настройке сертификатов клиента с помощью канала gRPC см. в разделе <xref:grpc/authn-and-authz#client-certificate-authentication>.

Чтобы вызвать незащищенные службы gRPC, убедитесь, что адрес сервера начинается с `http`. Например, `GrpcChannel.ForAddress("http://localhost:5000")` использует протокол HTTP. В .NET Core 3.1 или более поздней версии для [вызова незащищенных служб gRPC с помощью клиента .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client) требуется дополнительная настройка.

### <a name="client-performance"></a>Производительность клиента

Производительность и использование канала и клиента:

* Создание канала может потребовать значительных ресурсов. Повторное использование канала для вызовов gRPC обеспечивает выигрыш в производительности.
* Клиенты gRPC создаются с помощью каналов. Клиенты gRPC являются облегченными объектами и не нуждаются в кэшировании или повторном использовании.
* Из одного канала можно создать несколько клиентов gRPC, включая различные типы клиентов.
* Канал и клиенты, созданные из канала, могут безопасно использоваться несколькими потоками.
* Клиенты, созданные из канала, могут выполнять несколько одновременных вызовов.

`GrpcChannel.ForAddress` — не единственный вариант создания клиента gRPC. При вызове службы gRPC из приложения ASP.NET Core, рассмотрите возможность [интеграции фабрики клиента gRPC](xref:grpc/clientfactory). Интеграция gRPC с `HttpClientFactory` предлагает централизованную альтернативу созданию клиентов gRPC.

> [!NOTE]
> Вызов gRPC через HTTP/2 с `Grpc.Net.Client` в настоящее время не поддерживается в Xamarin. Мы работаем над улучшением поддержки HTTP/2 в будущих выпусках Xamarin. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) и [gRPC-Web](xref:grpc/browser) являются приемлемыми работающими альтернативами, которые доступны на сегодняшний день.

## <a name="make-grpc-calls"></a>Вызовы gRPC

Вызов gRPC инициируется путем вызова метода в клиенте. Клиент gRPC будет выполнять сериализацию сообщений и направлять вызов gRPC к правильной службе.

gRPC имеет различные типы методов. Способ использования клиента для выполнения вызова gRPC зависит от типа вызываемого метода. Типы методов gRPC:

* Унарный
* Потоковая передача сервера
* Потоковая передача клиента
* Двунаправленная потоковая передача

### <a name="unary-call"></a>Унарный вызов

Унарный вызов начинается с клиента, отправляющего сообщение с запросом. После завершения работы службы возвращается ответное сообщение.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Каждый унарный метод службы в файле *\*PROTO* приведет к появлению двух методов .NET в конкретном типе клиента gRPC для вызова метода: асинхронного метода и блокирующего метода. Например, в `GreeterClient` существует два способа вызова `SayHello`.

* `GreeterClient.SayHelloAsync` — асинхронный вызов службы `Greeter.SayHello`. Может быть ожидаемым.
* `GreeterClient.SayHello` — вызов службы `Greeter.SayHello` и блокировка до завершения. Не используйте его в асинхронном коде.

### <a name="server-streaming-call"></a>Вызов потоковой передачи сервера

Вызов потоковой передачи сервера начинается с клиента, отправляющего сообщение с запросом. `ResponseStream.MoveNext()` считывает сообщения, переданные в службу путем потоковой передачи. Вызов потоковой передачи сервера завершается, когда `ResponseStream.MoveNext()` возвращает `false`.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Если используется C# 8 или более поздней версии, для чтения сообщений можно использовать синтаксис `await foreach`. Метод расширения `IAsyncStreamReader<T>.ReadAllAsync()` считывает все сообщения из потока ответов:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Вызов потоковой передачи клиента

Вызов потоковой передачи клиента начинается *без* клиента, отправляющего сообщение с запросом. Клиент может выбрать отправку сообщений с помощью `RequestStream.WriteAsync`. Когда клиент завершит отправку сообщений, следует вызвать `RequestStream.CompleteAsync()`, чтобы уведомить службу. Вызов завершается, когда служба возвращает ответное сообщение.

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a>Вызов двунаправленной потоковой передачи

Вызов двунаправленной потоковой передачи начинается *без* клиента, отправляющего сообщение с запросом. Клиент может выбрать отправку сообщений с помощью `RequestStream.WriteAsync`. Сообщения, переданные в службу путем потоковой передачи, доступны с `ResponseStream.MoveNext()` или `ResponseStream.ReadAllAsync()`. Вызов двунаправленной потоковой передачи завершается, когда `ResponseStream` больше не содержит сообщений.

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

Чтобы обеспечить наилучшую производительность и избежать ненужных ошибок в клиенте и службе, старайтесь правильно выполнять двунаправленные потоковые вызовы. Двунаправленный вызов завершается корректно, когда сервер завершил чтение потока запроса, а клиент завершил чтение потока ответа. Предыдущий пример вызова — это один из примеров двунаправленного вызова, который завершается корректно. При вызове клиент:

1. Запускает новый двунаправленный потоковый вызов путем вызова `EchoClient.Echo`.
2. Создает фоновую задачу для чтения сообщений из службы с помощью `ResponseStream.ReadAllAsync()`.
3. Отправляет сообщения на сервер с помощью `RequestStream.WriteAsync`.
4. Сообщает серверу, что он закончил отправку сообщений с помощью `RequestStream.CompleteAsync()`.
5. Ожидает, пока фоновая задача не прочитает все входящие сообщения.

Во время вызова двунаправленной потоковой передачи клиент и служба могут обмениваться сообщениями в любое время. Наиболее подходящая логика клиента для взаимодействия с вызовом двунаправленной потоковой передачи зависит от логики службы.

## <a name="access-grpc-headers"></a>Доступ к заголовкам gRPC

Вызовы gRPC возвращают заголовки ответа. Заголовки ответа HTTP передают метаданные (имя и значение) вызова без связи с возвращаемым сообщением.

Заголовки gRPC доступны при использовании `ResponseHeadersAsync` (возвращает коллекцию метаданных). Заголовки обычно возвращаются с ответным сообщением. Поэтому их необходимо ожидать.

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

При использовании `ResponseHeadersAsync`:

* Нужно дождаться результата `ResponseHeadersAsync`, чтобы получить коллекцию заголовков.
* Не нужно получать доступ перед `ResponseAsync` (или потока ответа при потоковой передаче). Если ответ был получен, `ResponseHeadersAsync` немедленно возвращает заголовки.
* Выдается исключение, если возникла ошибка подключения или сервера и для вызова gRPC заголовки не получены.

## <a name="access-grpc-trailers"></a>Доступ к трейлерам gRPC

Вызовы gRPC могут возвращать трейлеры ответа. Трейлеры содержат метаданные (имя и значение) вызова. Трейлеры содержат аналогичные функции для заголовков HTTP, но они принимаются в конце вызова.

Трейлеры доступны при использовании `GetTrailers()` (возвращает коллекцию метаданных). Трейлеры возвращаются после завершения ответа. Поэтому перед обращением к трейлеру необходимо дождаться всех ответных сообщений.

Унарные и клиентские вызовы потоковой передачи должны дождаться `ResponseAsync` перед вызовом `GetTrailers()`:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Вызовы сервера и двунаправленной потоковой передачи должны завершить ожидание ответного потока перед вызовом `GetTrailers()`:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Трейлеры также доступны из `RpcException`. Служба может вернуть трейлеры вместе со статусом gRPC, отличным от "ОК". В этой ситуации трейлеры извлекаются из исключения, вызываемого клиентом gRPC:

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a>Настройка крайнего срока

Настраивать крайний срок в вызове gRPC рекомендуется по той причине, что он ограничивает длительность выполнения вызова. Это позволяет предотвратить бесконечное выполнение служб и исчерпание ресурсов сервера. Крайние сроки — это полезное средство для повышения надежности приложений.

Чтобы задать крайний срок для вызова gRPC, настройте `CallOptions.Deadline`:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Для получения дополнительной информации см. <xref:grpc/deadlines-cancellation#deadlines>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
