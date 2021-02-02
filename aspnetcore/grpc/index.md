---
title: Общие сведения об использовании gRPC на платформе .NET
author: juntaoluo
description: Узнайте об использовании служб gRPC с сервером Kestrel и стеком ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 5820049aba90a2fbd06a23756b12ac9656c3b2c4
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057516"
---
# <a name="introduction-to-grpc-on-net"></a>Общие сведения об использовании gRPC на платформе .NET

Авторы: [Джон Луо](https://github.com/juntaoluo) (John Luo) и [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

[gRPC](https://grpc.io/docs/guides/) — это не зависящая от языка высокопроизводительная платформа удаленного вызова процедур (RPC).

Ниже приведены основные преимущества gRPC.
* Современная высокопроизводительная упрощенная платформа RPC.
* Разработка API по модели "сначала контракт" с использованием механизма Protocol Buffers по умолчанию, что позволяет выпускать не зависящие от языка реализации.
* Доступные для многих языков инструменты, предназначенные для создания строго типизированных серверов и клиентов.
* Поддержка клиентских, серверных и двунаправленных потоковых вызовов.
* Снижение уровня использования сети за счет двоичной сериализации Protobuf.

Благодаря этим преимуществам gRPC идеально подходит для:
* упрощенных микрослужб, где важна эффективность;
* многоязычных систем, где для разработки требуется несколько языков;
* работающих в режиме реального времени служб типа "точка-точка", которые должны обрабатывать запросы и ответы потоковой передачи данных.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>Средства C# для работы с файлами с расширением .proto

Для разработки API в gRPC используется подход, при котором сначала создается контракт. Службы и сообщения определяются в файлах *\*.proto*:

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Типы .NET для служб, клиентов и сообщений создаются автоматически, путем добавления файлов *\*.proto* в проект:

* Добавьте ссылку на пакет [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).
* Добавьте файлы *\*.proto* в группу элементов `<Protobuf>`.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Дополнительные сведения о поддержке средств gRPC см. в статье <xref:grpc/basics>.

## <a name="grpc-services-on-aspnet-core"></a>Службы gRPC на платформе ASP.NET Core

Службы gRPC можно размещать на платформе ASP.NET Core. Службы поддерживают полную интеграцию с популярными функциями ASP.NET Core, такими как ведение журнала, внедрение зависимостей (DI), проверка подлинности и авторизация.

В шаблоне проекта gRPC предоставляется базовая служба:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService` является производным от типа `GreeterBase`, который создается из службы `Greeter` в файле *\*.proto*. Служба становится доступной для клиентов в *Startup.cs*.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Дополнительные сведения о службах gRPC на ASP.NET Core см. в статье <xref:grpc/aspnetcore>.

## <a name="call-grpc-services-with-a-net-client"></a>Вызов служб gRPC с помощью клиента .NET

Клиенты gRPC являются конкретными типами клиентов, [создаваемыми в файлах *\*.proto*](xref:grpc/basics#generated-c-assets). Конкретный клиент gRPC использует методы, которые выполняют преобразование для служб gRPC в файле *\*.proto*.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Клиент gRPC создается с помощью канала, который представляет длительное подключение к службе gRPC. Канал можно создать с помощью `GrpcChannel.ForAddress`.

Дополнительные сведения о создании клиентов и вызове различных методов службы см. в статье <xref:grpc/client>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
