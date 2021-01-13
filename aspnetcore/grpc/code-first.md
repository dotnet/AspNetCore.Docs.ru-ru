---
title: Создание клиентов и служб gRPC с поддержкой Code First с помощью .NET
author: jamesnk
description: Сведения об основных понятиях при написании служб gRPC с поддержкой Code First с помощью .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880623"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>Создание клиентов и служб gRPC с поддержкой Code First с помощью .NET

Авторы: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King) и [Марк Гравелл](https://twitter.com/marcgravell) (Marc Gravell)

Решения GRPC с поддержкой подхода Code First используют типы .NET для определения контрактов служб и сообщений.

Code First — это хороший вариант, когда вся система использует .NET:

* служба .NET и типы контрактов данных могут совместно использоваться клиентами и сервером .NET;
* не требуется определять контракты в файлах `.proto` и создаваемом коде.

Code First не рекомендуется использовать в системах с поддержкой нескольких языков. Службы .NET и типы контрактов данных нельзя использовать с платформами, отличающимися от .NET. Для вызова службы gRPC, написанной с применением подхода Code First, на других платформах необходимо создать контракт `.proto`, соответствующий службе.

## <a name="protobuf-netgrpc"></a>protobuf-net.Grpc

> [!IMPORTANT]
> Чтобы получить справку по protobuf-net.Grpc, перейдите [сюда](https://protobuf-net.github.io/protobuf-net.Grpc/) или создайте соответствующий запрос в [репозитории protobuf-net.Grpc в GitHub](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) — это проект сообщества, который не поддерживается корпорацией Майкрософт. Он позволяет включить поддержку Code First в`Grpc.AspNetCore` и `Grpc.Net.Client`. В нем используются типы .NET, помеченные атрибутами для определения сообщений и служб gRPC приложения.

Первый шаг в создании службы gRPC с поддержкой Code First — определение контракта кода:

* Создайте новый проект, который будет совместно использоваться сервером и клиентом.
* Добавьте ссылку на пакет [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).
* Создайте типы контрактов данных и служб.

[!code-csharp[](code-first/Contracts.cs)]

Предыдущий код:

* Определяет сообщения `HelloRequest` и `HelloReply`.
* Определяет интерфейс контракта `IGreeterService` с помощью унарного метода gRPC `SayHelloAsync`.

Контракт службы реализован на сервере и вызывается из клиента. Методы, определенные в интерфейсах служб, должны соответствовать определенным сигнатурам в зависимости от того, являются ли они унарными либо представляют серверную потоковую передачу, клиентскую потоковую передачу или двунаправленную потоковую передачу.

Дополнительные сведения об определении контрактов служб см. в [документации по началу работы с protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Создание службы gRPC с поддержкой Code First

Чтобы добавить службу gRPC с поддержкой Code First в приложение ASP.NET Core:

* Добавьте ссылку на пакет [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore).
* Добавьте ссылку на проект контракта с общим кодом.

Создайте новый файл `GreeterService.cs` и реализуйте интерфейс службы `IGreeterService`:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Обновите файл `Startup.cs`:

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

В предыдущем коде:

* `AddCodeFirstGrpc` регистрирует службы с поддержкой Code First.
* `MapGrpcService<GreeterService>` добавляет конечную точку службы с поддержкой Code First.

Службы gRPC, реализованные с применением подхода Code First и файлов `.proto`, могут сосуществовать в одном приложении. Все службы gRPC используют [конфигурацию службы gRPC](xref:grpc/configuration#configure-services-options).

## <a name="create-a-code-first-grpc-client"></a>Создание клиента gRPC с поддержкой Code First

Клиент gRPC с поддержкой Code First использует контракт службы для вызова служб gRPC. Чтобы вызвать службу gRPC с применением подхода Code First:

* Добавьте ссылку на пакет [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).
* Добавьте ссылку на проект контракта с общим кодом.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

Предыдущий код:

* Создает канал gRPC.
* Создает клиент с поддержкой Code First из канала с помощью метода расширения `CreateGrpcService<IGreeterService>`.
* Вызывает службу gRPC с использованием `SayHelloAsync`.

В канале создается клиент gRPC с поддержкой Code First. Как и обычный клиент, клиент с поддержкой Code First использует свою [конфигурацию канала](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Веб-сайт protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [Репозиторий protobuf-net.Grpc в GitHub](https://github.com/protobuf-net/protobuf-net.Grpc)
