---
title: Межпроцессное взаимодействие с помощью gRPC
author: jamesnk
description: Узнайте, как использовать gRPC для межпроцессного взаимодействия.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: 8c0f8fb1468e61d5aa2e7f42cb5da33c01819124
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217470"
---
# <a name="inter-process-communication-with-grpc"></a>Межпроцессное взаимодействие с помощью gRPC

Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)

Вызовы gRPC между клиентом и службой обычно отправляются через сокеты TCP. Протокол TCP был разработан для обмена данными по сети. [Межпроцессное взаимодействие (IPC)](https://wikipedia.org/wiki/Inter-process_communication) более эффективно, чем TCP, если клиент и служба находятся на одном компьютере. В этом документе объясняется, как использовать gRPC с настраиваемым транспортом в сценариях IPC.

## <a name="server-configuration"></a>Конфигурация сервера

[Kestrel](xref:fundamentals/servers/kestrel) поддерживает настраиваемый транспорт. Kestrel настраивается в файле *Program.cs*:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

Предшествующий пример:

* Настраивает конечные точки Kestrel в `ConfigureKestrel`.
* Вызывает <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> для прослушивания [сокета домена UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) по указанному пути.

Kestrel имеет встроенную поддержку конечных точек UDS. UDS поддерживаются в операционных системах Linux, macOS и [современных версиях Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Настройка клиента

`GrpcChannel` поддерживает выполнение вызовов gRPC через настраиваемый транспорт. При создании канала его можно настроить с помощью `SocketsHttpHandler`, у которого имеется настраиваемый `ConnectCallback`. Обратный вызов позволяет клиенту устанавливать соединения через настраиваемые транспорты, а затем передавать HTTP-запросы через этот транспорт.

Пример фабрики подключений для сокетов доменов UNIX:

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

Использование настраиваемой фабрики подключений для создания канала:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Каналы, созданные с помощью приведенного выше кода, отправляют вызовы gRPC через сокеты доменов UNIX. Поддержку других технологий IPC можно реализовать с помощью расширяемости в Kestrel и `SocketsHttpHandler`.
