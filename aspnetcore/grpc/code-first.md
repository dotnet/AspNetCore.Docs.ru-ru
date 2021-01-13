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
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="da5f3-103">Создание клиентов и служб gRPC с поддержкой Code First с помощью .NET</span><span class="sxs-lookup"><span data-stu-id="da5f3-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="da5f3-104">Авторы: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King) и [Марк Гравелл](https://twitter.com/marcgravell) (Marc Gravell)</span><span class="sxs-lookup"><span data-stu-id="da5f3-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="da5f3-105">Решения GRPC с поддержкой подхода Code First используют типы .NET для определения контрактов служб и сообщений.</span><span class="sxs-lookup"><span data-stu-id="da5f3-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="da5f3-106">Code First — это хороший вариант, когда вся система использует .NET:</span><span class="sxs-lookup"><span data-stu-id="da5f3-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="da5f3-107">служба .NET и типы контрактов данных могут совместно использоваться клиентами и сервером .NET;</span><span class="sxs-lookup"><span data-stu-id="da5f3-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="da5f3-108">не требуется определять контракты в файлах `.proto` и создаваемом коде.</span><span class="sxs-lookup"><span data-stu-id="da5f3-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="da5f3-109">Code First не рекомендуется использовать в системах с поддержкой нескольких языков.</span><span class="sxs-lookup"><span data-stu-id="da5f3-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="da5f3-110">Службы .NET и типы контрактов данных нельзя использовать с платформами, отличающимися от .NET.</span><span class="sxs-lookup"><span data-stu-id="da5f3-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="da5f3-111">Для вызова службы gRPC, написанной с применением подхода Code First, на других платформах необходимо создать контракт `.proto`, соответствующий службе.</span><span class="sxs-lookup"><span data-stu-id="da5f3-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="da5f3-112">protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="da5f3-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="da5f3-113">Чтобы получить справку по protobuf-net.Grpc, перейдите [сюда](https://protobuf-net.github.io/protobuf-net.Grpc/) или создайте соответствующий запрос в [репозитории protobuf-net.Grpc в GitHub](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="da5f3-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="da5f3-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) — это проект сообщества, который не поддерживается корпорацией Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="da5f3-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="da5f3-115">Он позволяет включить поддержку Code First в`Grpc.AspNetCore` и `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="da5f3-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="da5f3-116">В нем используются типы .NET, помеченные атрибутами для определения сообщений и служб gRPC приложения.</span><span class="sxs-lookup"><span data-stu-id="da5f3-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="da5f3-117">Первый шаг в создании службы gRPC с поддержкой Code First — определение контракта кода:</span><span class="sxs-lookup"><span data-stu-id="da5f3-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="da5f3-118">Создайте новый проект, который будет совместно использоваться сервером и клиентом.</span><span class="sxs-lookup"><span data-stu-id="da5f3-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="da5f3-119">Добавьте ссылку на пакет [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="da5f3-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="da5f3-120">Создайте типы контрактов данных и служб.</span><span class="sxs-lookup"><span data-stu-id="da5f3-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="da5f3-121">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="da5f3-121">The preceding code:</span></span>

* <span data-ttu-id="da5f3-122">Определяет сообщения `HelloRequest` и `HelloReply`.</span><span class="sxs-lookup"><span data-stu-id="da5f3-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="da5f3-123">Определяет интерфейс контракта `IGreeterService` с помощью унарного метода gRPC `SayHelloAsync`.</span><span class="sxs-lookup"><span data-stu-id="da5f3-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="da5f3-124">Контракт службы реализован на сервере и вызывается из клиента.</span><span class="sxs-lookup"><span data-stu-id="da5f3-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="da5f3-125">Методы, определенные в интерфейсах служб, должны соответствовать определенным сигнатурам в зависимости от того, являются ли они унарными либо представляют серверную потоковую передачу, клиентскую потоковую передачу или двунаправленную потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="da5f3-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="da5f3-126">Дополнительные сведения об определении контрактов служб см. в [документации по началу работы с protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="da5f3-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="da5f3-127">Создание службы gRPC с поддержкой Code First</span><span class="sxs-lookup"><span data-stu-id="da5f3-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="da5f3-128">Чтобы добавить службу gRPC с поддержкой Code First в приложение ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="da5f3-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="da5f3-129">Добавьте ссылку на пакет [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="da5f3-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="da5f3-130">Добавьте ссылку на проект контракта с общим кодом.</span><span class="sxs-lookup"><span data-stu-id="da5f3-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="da5f3-131">Создайте новый файл `GreeterService.cs` и реализуйте интерфейс службы `IGreeterService`:</span><span class="sxs-lookup"><span data-stu-id="da5f3-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="da5f3-132">Обновите файл `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="da5f3-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="da5f3-133">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="da5f3-133">In the preceding code:</span></span>

* <span data-ttu-id="da5f3-134">`AddCodeFirstGrpc` регистрирует службы с поддержкой Code First.</span><span class="sxs-lookup"><span data-stu-id="da5f3-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="da5f3-135">`MapGrpcService<GreeterService>` добавляет конечную точку службы с поддержкой Code First.</span><span class="sxs-lookup"><span data-stu-id="da5f3-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="da5f3-136">Службы gRPC, реализованные с применением подхода Code First и файлов `.proto`, могут сосуществовать в одном приложении.</span><span class="sxs-lookup"><span data-stu-id="da5f3-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="da5f3-137">Все службы gRPC используют [конфигурацию службы gRPC](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="da5f3-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="da5f3-138">Создание клиента gRPC с поддержкой Code First</span><span class="sxs-lookup"><span data-stu-id="da5f3-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="da5f3-139">Клиент gRPC с поддержкой Code First использует контракт службы для вызова служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="da5f3-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="da5f3-140">Чтобы вызвать службу gRPC с применением подхода Code First:</span><span class="sxs-lookup"><span data-stu-id="da5f3-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="da5f3-141">Добавьте ссылку на пакет [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="da5f3-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="da5f3-142">Добавьте ссылку на проект контракта с общим кодом.</span><span class="sxs-lookup"><span data-stu-id="da5f3-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="da5f3-143">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="da5f3-143">The preceding code:</span></span>

* <span data-ttu-id="da5f3-144">Создает канал gRPC.</span><span class="sxs-lookup"><span data-stu-id="da5f3-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="da5f3-145">Создает клиент с поддержкой Code First из канала с помощью метода расширения `CreateGrpcService<IGreeterService>`.</span><span class="sxs-lookup"><span data-stu-id="da5f3-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="da5f3-146">Вызывает службу gRPC с использованием `SayHelloAsync`.</span><span class="sxs-lookup"><span data-stu-id="da5f3-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="da5f3-147">В канале создается клиент gRPC с поддержкой Code First.</span><span class="sxs-lookup"><span data-stu-id="da5f3-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="da5f3-148">Как и обычный клиент, клиент с поддержкой Code First использует свою [конфигурацию канала](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="da5f3-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da5f3-149">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="da5f3-149">Additional resources</span></span>

* [<span data-ttu-id="da5f3-150">Веб-сайт protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="da5f3-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="da5f3-151">Репозиторий protobuf-net.Grpc в GitHub</span><span class="sxs-lookup"><span data-stu-id="da5f3-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
