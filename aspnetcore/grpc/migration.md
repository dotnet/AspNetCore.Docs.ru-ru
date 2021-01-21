---
title: Миграция служб gRPC из C-Core в ASP.NET Core
author: juntaoluo
description: Узнайте, как переместить существующее приложение gRPC на основе C-Core для выполнения поверх стека ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 27c53dd4b41d6c99e45fccb5af79bab1ed5dc1b9
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253154"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Миграция служб gRPC из C-Core в ASP.NET Core

Автор: [John Luo](https://github.com/juntaoluo) (Джон Луо)

В связи с реализацией базового стека не все функции работают одинаково в приложениях [gRPC на основе C-Core](https://grpc.io/blog/grpc-stacks) и приложениях на базе ASP.NET Core. В этом документе описываются основные различия между двумя стеками в целях миграции.

## <a name="grpc-service-implementation-lifetime"></a>Время существования реализации службы gRPC

В стеке ASP.NET Core службы gRPC по умолчанию создаются с [ограниченным временем существования](xref:fundamentals/dependency-injection#service-lifetimes). В отличие от этого, gRPC C-Core по умолчанию привязывается к службе с [отдельным временем существования](xref:fundamentals/dependency-injection#service-lifetimes).

Ограниченное время существования позволяет реализации службы разрешать другие службы с ограниченным временем существования. Например, ограниченное время существования может также разрешать `DbContext` из контейнера DI посредством внедрения конструктора. Использование ограниченного времени существования:

* Новый экземпляр реализации службы создается для каждого запроса.
* Невозможно совместно использовать состояние между запросами через члены экземпляра в типе реализации.
* Общие состояния должны храниться в отдельной службе в контейнере DI. Хранимые общие состояния разрешаются в конструкторе реализации службы gRPC.

Дополнительные сведения о времени существования см. в разделе <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Добавление отдельной службы

Чтобы упростить переход от реализации gRPC C-Core к ASP.NET Core, можно изменить время существования реализации службы с ограниченного на отдельное. Для этого необходимо добавить экземпляр реализации службы в контейнер DI:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Однако реализация службы с отдельным временем существования больше не может разрешать службы с заданной областью посредством внедрения конструктора.

## <a name="configure-grpc-services-options"></a>Настройка параметров служб gRPC

В приложениях на основе C-Core такие параметры, как `grpc.max_receive_message_length` и `grpc.max_send_message_length`, настраиваются с помощью `ChannelOption` при [создании экземпляра сервера](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).

В ASP.NET Core gRPC предоставляет конфигурацию с помощью типа `GrpcServiceOptions`. Например, максимальный размер входящего сообщения службы gRPC можно настроить с помощью `AddGrpc`. В следующем примере показано изменение `MaxReceiveMessageSize` по умолчанию с 4 на 16 МБ:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Дополнительные сведения о настройке см. в разделе <xref:grpc/configuration>.

## <a name="logging"></a>Ведение журнала

Приложения на основе C-Core используют `GrpcEnvironment` для [настройки средства ведения журнала](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) в целях отладки. Стек ASP.NET Core обеспечивает эти функции с помощью [API ведения журнала](xref:fundamentals/logging/index). Например, средство ведения журнала можно добавить в службу gRPC посредством внедрения конструктора:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

::: moniker range=">= aspnetcore-5.0"
Приложения на основе C-Core настраивают HTTPS через [свойство Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Аналогичная концепция используется для настройки серверов в ASP.NET Core. Например, для этой функции Kestrel использует [конфигурацию конечной точки](xref:fundamentals/servers/kestrel/endpoints).
::: moniker-end

::: moniker range="< aspnetcore-5.0"
Приложения на основе C-Core настраивают HTTPS через [свойство Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports). Аналогичная концепция используется для настройки серверов в ASP.NET Core. Например, для этой функции Kestrel использует [конфигурацию конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).
::: moniker-end

## <a name="grpc-interceptors-vs-middleware"></a>ПО промежуточного слоя и перехватчики gRPC

[ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core предлагает аналогичные функциональные возможности по сравнению с перехватчиками в приложениях gRPC на основе C-Core. ПО промежуточного слоя ASP.NET Core и перехватчики выполняют одни и те же задачи. Оба.

* Используются для создания конвейера, обрабатывающего запрос gRPC.
* Позволяют выполнять работу как до, так и после вызова следующего компонента в конвейере.
* Предоставляют доступ к `HttpContext`:
  * В ПО промежуточного слоя `HttpContext` является параметром.
  * В перехватчиках доступ к `HttpContext` можно получить с помощью параметра `ServerCallContext` с методом расширения `ServerCallContext.GetHttpContext`. Обратите внимание, что эта функция относится к перехватчикам, выполняемым в ASP.NET Core.

Отличия перехватчиков gRPC от ПО промежуточного слоя ASP.NET Core:

* Перехватчики:
  * Работают на уровне абстракции gRPC с использованием [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * Предоставляют доступ к:
    * Десериализованному сообщению, которое отправлено вызову.
    * Сообщению, возвращаемому из вызова перед его сериализацией.
  * Могут перехватывать и обрабатывать исключения, созданные службами gRPC.
* ПО промежуточного слоя:
  * Выполняется перед перехватчиками gRPC.
  * Работает с базовыми сообщениями HTTP/2.
  * Может получать доступ только к байтам из потоков запросов и ответов.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
