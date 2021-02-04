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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="365e8-103">Межпроцессное взаимодействие с помощью gRPC</span><span class="sxs-lookup"><span data-stu-id="365e8-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="365e8-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="365e8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="365e8-105">Вызовы gRPC между клиентом и службой обычно отправляются через сокеты TCP.</span><span class="sxs-lookup"><span data-stu-id="365e8-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="365e8-106">Протокол TCP был разработан для обмена данными по сети.</span><span class="sxs-lookup"><span data-stu-id="365e8-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="365e8-107">[Межпроцессное взаимодействие (IPC)](https://wikipedia.org/wiki/Inter-process_communication) более эффективно, чем TCP, если клиент и служба находятся на одном компьютере.</span><span class="sxs-lookup"><span data-stu-id="365e8-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="365e8-108">В этом документе объясняется, как использовать gRPC с настраиваемым транспортом в сценариях IPC.</span><span class="sxs-lookup"><span data-stu-id="365e8-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="365e8-109">Конфигурация сервера</span><span class="sxs-lookup"><span data-stu-id="365e8-109">Server configuration</span></span>

<span data-ttu-id="365e8-110">[Kestrel](xref:fundamentals/servers/kestrel) поддерживает настраиваемый транспорт.</span><span class="sxs-lookup"><span data-stu-id="365e8-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="365e8-111">Kestrel настраивается в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="365e8-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="365e8-112">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="365e8-112">The preceding example:</span></span>

* <span data-ttu-id="365e8-113">Настраивает конечные точки Kestrel в `ConfigureKestrel`.</span><span class="sxs-lookup"><span data-stu-id="365e8-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="365e8-114">Вызывает <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> для прослушивания [сокета домена UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="365e8-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="365e8-115">Kestrel имеет встроенную поддержку конечных точек UDS.</span><span class="sxs-lookup"><span data-stu-id="365e8-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="365e8-116">UDS поддерживаются в операционных системах Linux, macOS и [современных версиях Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="365e8-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="365e8-117">Настройка клиента</span><span class="sxs-lookup"><span data-stu-id="365e8-117">Client configuration</span></span>

<span data-ttu-id="365e8-118">`GrpcChannel` поддерживает выполнение вызовов gRPC через настраиваемый транспорт.</span><span class="sxs-lookup"><span data-stu-id="365e8-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="365e8-119">При создании канала его можно настроить с помощью `SocketsHttpHandler`, у которого имеется настраиваемый `ConnectCallback`.</span><span class="sxs-lookup"><span data-stu-id="365e8-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="365e8-120">Обратный вызов позволяет клиенту устанавливать соединения через настраиваемые транспорты, а затем передавать HTTP-запросы через этот транспорт.</span><span class="sxs-lookup"><span data-stu-id="365e8-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

<span data-ttu-id="365e8-121">Пример фабрики подключений для сокетов доменов UNIX:</span><span class="sxs-lookup"><span data-stu-id="365e8-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="365e8-122">Использование настраиваемой фабрики подключений для создания канала:</span><span class="sxs-lookup"><span data-stu-id="365e8-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="365e8-123">Каналы, созданные с помощью приведенного выше кода, отправляют вызовы gRPC через сокеты доменов UNIX.</span><span class="sxs-lookup"><span data-stu-id="365e8-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="365e8-124">Поддержку других технологий IPC можно реализовать с помощью расширяемости в Kestrel и `SocketsHttpHandler`.</span><span class="sxs-lookup"><span data-stu-id="365e8-124">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
