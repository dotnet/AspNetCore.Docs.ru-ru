---
title: Использование концентраторов в ASP.NET Core SignalR
author: bradygaster
description: Узнайте, как использовать концентраторы в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 872b88cc3c87137365de8c50a37bf5dd5fd9fe10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587870"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="c5af4-103">Использование концентраторов в SignalR для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5af4-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="c5af4-104">[Рейчел Аппель (](https://twitter.com/rachelappel) и [Кевин Гриффин](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="c5af4-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="c5af4-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c5af4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="c5af4-106">Что такое SignalR центр</span><span class="sxs-lookup"><span data-stu-id="c5af4-106">What is a SignalR hub</span></span>

<span data-ttu-id="c5af4-107">SignalRAPI концентраторов позволяет вызывать методы на подключенных клиентах с сервера.</span><span class="sxs-lookup"><span data-stu-id="c5af4-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="c5af4-108">В серверном коде определяются методы, вызываемые клиентом.</span><span class="sxs-lookup"><span data-stu-id="c5af4-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="c5af4-109">В клиентском коде определяются методы, которые вызываются с сервера.</span><span class="sxs-lookup"><span data-stu-id="c5af4-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="c5af4-110">SignalR позаботится обо всем, за сценой, которая делает возможными обмен данными между клиентом и сервером в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="c5af4-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="c5af4-111">Настройка SignalR концентраторов</span><span class="sxs-lookup"><span data-stu-id="c5af4-111">Configure SignalR hubs</span></span>

<span data-ttu-id="c5af4-112">По SignalR промежуточного слоя требуются некоторые службы, которые настраиваются путем вызова `services.AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c5af4-113">При добавлении SignalR функциональных возможностей в ASP.NET Core приложение настройте SignalR маршруты путем вызова `endpoint.MapHub` в `Startup.Configure` `app.UseEndpoints` обратном вызове метода.</span><span class="sxs-lookup"><span data-stu-id="c5af4-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c5af4-114">При добавлении SignalR функциональных возможностей в ASP.NET Core приложение настройте SignalR маршруты путем вызова `app.UseSignalR` `Startup.Configure` метода в методе.</span><span class="sxs-lookup"><span data-stu-id="c5af4-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="c5af4-115">Создание и использование концентраторов</span><span class="sxs-lookup"><span data-stu-id="c5af4-115">Create and use hubs</span></span>

<span data-ttu-id="c5af4-116">Создайте центр, объявив класс, наследующий от `Hub` , и добавьте в него открытые методы.</span><span class="sxs-lookup"><span data-stu-id="c5af4-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="c5af4-117">Клиенты могут вызывать методы, определенные как `public` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="c5af4-118">Можно указать тип возвращаемого значения и параметры, включая сложные типы и массивы, как в любом методе C#.</span><span class="sxs-lookup"><span data-stu-id="c5af4-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="c5af4-119">SignalR обрабатывает сериализацию и десериализацию сложных объектов и массивов в параметрах и возвращаемых значениях.</span><span class="sxs-lookup"><span data-stu-id="c5af4-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="c5af4-120">Концентраторы являются временными:</span><span class="sxs-lookup"><span data-stu-id="c5af4-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="c5af4-121">Не сохраняйте состояние в свойстве класса Hub.</span><span class="sxs-lookup"><span data-stu-id="c5af4-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="c5af4-122">Каждый вызов метода концентратора выполняется в новом экземпляре концентратора.</span><span class="sxs-lookup"><span data-stu-id="c5af4-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="c5af4-123">Используется `await` при вызове асинхронных методов, зависящих от концентратора.</span><span class="sxs-lookup"><span data-stu-id="c5af4-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="c5af4-124">Например, метод `Clients.All.SendAsync(...)` может завершиться ошибкой, если он вызывается без `await` , а метод концентратора завершается до `SendAsync` завершения.</span><span class="sxs-lookup"><span data-stu-id="c5af4-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="c5af4-125">Контекстный объект</span><span class="sxs-lookup"><span data-stu-id="c5af4-125">The Context object</span></span>

<span data-ttu-id="c5af4-126">`Hub`Класс имеет `Context` свойство, которое содержит следующие свойства со сведениями о соединении:</span><span class="sxs-lookup"><span data-stu-id="c5af4-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="c5af4-127">Свойство</span><span class="sxs-lookup"><span data-stu-id="c5af4-127">Property</span></span> | <span data-ttu-id="c5af4-128">Описание</span><span class="sxs-lookup"><span data-stu-id="c5af4-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="c5af4-129">Возвращает уникальный идентификатор соединения, присвоенный SignalR .</span><span class="sxs-lookup"><span data-stu-id="c5af4-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="c5af4-130">Для каждого соединения существует один идентификатор подключения.</span><span class="sxs-lookup"><span data-stu-id="c5af4-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="c5af4-131">Возвращает [идентификатор пользователя](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="c5af4-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="c5af4-132">По умолчанию SignalR использует `ClaimTypes.NameIdentifier` из из, `ClaimsPrincipal` связанного с соединением, в качестве идентификатора пользователя.</span><span class="sxs-lookup"><span data-stu-id="c5af4-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="c5af4-133">Возвращает объект, `ClaimsPrincipal` связанный с текущим пользователем.</span><span class="sxs-lookup"><span data-stu-id="c5af4-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="c5af4-134">Возвращает коллекцию "ключ-значение", которую можно использовать для совместного использования данных в области этого соединения.</span><span class="sxs-lookup"><span data-stu-id="c5af4-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="c5af4-135">Данные могут храниться в этой коллекции, и они будут сохранены для подключения через разные вызовы метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="c5af4-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="c5af4-136">Возвращает коллекцию функций, доступных в соединении.</span><span class="sxs-lookup"><span data-stu-id="c5af4-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="c5af4-137">Сейчас эта коллекция не требуется в большинстве сценариев, поэтому она еще не документирована.</span><span class="sxs-lookup"><span data-stu-id="c5af4-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="c5af4-138">Возвращает объект `CancellationToken` , уведомляющий о прерывании соединения.</span><span class="sxs-lookup"><span data-stu-id="c5af4-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="c5af4-139">`Hub.Context` также содержит следующие методы.</span><span class="sxs-lookup"><span data-stu-id="c5af4-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="c5af4-140">Метод</span><span class="sxs-lookup"><span data-stu-id="c5af4-140">Method</span></span> | <span data-ttu-id="c5af4-141">Описание</span><span class="sxs-lookup"><span data-stu-id="c5af4-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="c5af4-142">Возвращает `HttpContext` для соединения или, `null` Если соединение не СВЯЗАНО с HTTP-запросом.</span><span class="sxs-lookup"><span data-stu-id="c5af4-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="c5af4-143">Для HTTP-соединений этот метод можно использовать для получения таких сведений, как заголовки HTTP и строки запросов.</span><span class="sxs-lookup"><span data-stu-id="c5af4-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="c5af4-144">Прерывает подключение.</span><span class="sxs-lookup"><span data-stu-id="c5af4-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="c5af4-145">Объект «клиенты»</span><span class="sxs-lookup"><span data-stu-id="c5af4-145">The Clients object</span></span>

<span data-ttu-id="c5af4-146">`Hub`Класс имеет `Clients` свойство, которое содержит следующие свойства для обмена данными между сервером и клиентом:</span><span class="sxs-lookup"><span data-stu-id="c5af4-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="c5af4-147">Свойство</span><span class="sxs-lookup"><span data-stu-id="c5af4-147">Property</span></span> | <span data-ttu-id="c5af4-148">Описание</span><span class="sxs-lookup"><span data-stu-id="c5af4-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="c5af4-149">Вызывает метод для всех подключенных клиентов</span><span class="sxs-lookup"><span data-stu-id="c5af4-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="c5af4-150">Вызывает метод для клиента, который вызвал метод концентратора</span><span class="sxs-lookup"><span data-stu-id="c5af4-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="c5af4-151">Вызывает метод для всех подключенных клиентов, за исключением клиента, который вызвал метод</span><span class="sxs-lookup"><span data-stu-id="c5af4-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="c5af4-152">`Hub.Clients` также содержит следующие методы.</span><span class="sxs-lookup"><span data-stu-id="c5af4-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="c5af4-153">Метод</span><span class="sxs-lookup"><span data-stu-id="c5af4-153">Method</span></span> | <span data-ttu-id="c5af4-154">Описание</span><span class="sxs-lookup"><span data-stu-id="c5af4-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="c5af4-155">Вызывает метод для всех подключенных клиентов, за исключением указанных соединений</span><span class="sxs-lookup"><span data-stu-id="c5af4-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="c5af4-156">Вызывает метод для указанного подключенного клиента</span><span class="sxs-lookup"><span data-stu-id="c5af4-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="c5af4-157">Вызывает метод для конкретных подключенных клиентов</span><span class="sxs-lookup"><span data-stu-id="c5af4-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="c5af4-158">Вызывает метод для всех соединений в указанной группе</span><span class="sxs-lookup"><span data-stu-id="c5af4-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="c5af4-159">Вызывает метод для всех соединений в указанной группе, за исключением указанных соединений</span><span class="sxs-lookup"><span data-stu-id="c5af4-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="c5af4-160">Вызывает метод для нескольких групп соединений</span><span class="sxs-lookup"><span data-stu-id="c5af4-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="c5af4-161">Вызывает метод для группы соединений, исключая клиента, вызвавшего метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="c5af4-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="c5af4-162">Вызывает метод для всех соединений, связанных с конкретным пользователем</span><span class="sxs-lookup"><span data-stu-id="c5af4-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="c5af4-163">Вызывает метод для всех подключений, связанных с указанными пользователями</span><span class="sxs-lookup"><span data-stu-id="c5af4-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="c5af4-164">Каждое свойство или метод в приведенных выше таблицах возвращает объект с `SendAsync` методом.</span><span class="sxs-lookup"><span data-stu-id="c5af4-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="c5af4-165">`SendAsync`Метод позволяет указать имя и параметры вызываемого клиентского метода.</span><span class="sxs-lookup"><span data-stu-id="c5af4-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="c5af4-166">Отправка сообщений клиентам</span><span class="sxs-lookup"><span data-stu-id="c5af4-166">Send messages to clients</span></span>

<span data-ttu-id="c5af4-167">Чтобы вызвать определенные клиенты, используйте свойства `Clients` объекта.</span><span class="sxs-lookup"><span data-stu-id="c5af4-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="c5af4-168">В следующем примере есть три метода концентратора:</span><span class="sxs-lookup"><span data-stu-id="c5af4-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="c5af4-169">`SendMessage` отправляет сообщение всем подключенным клиентам с помощью `Clients.All` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="c5af4-170">`SendMessageToCaller` отправляет сообщение обратно вызывающему объекту с помощью `Clients.Caller` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="c5af4-171">`SendMessageToGroups` отправляет сообщение всем клиентам в `SignalR Users` группе.</span><span class="sxs-lookup"><span data-stu-id="c5af4-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="c5af4-172">Строго типизированные концентраторы</span><span class="sxs-lookup"><span data-stu-id="c5af4-172">Strongly typed hubs</span></span>

<span data-ttu-id="c5af4-173">Недостаток использования заключается в `SendAsync` том, что он использует волшебную строку для указания вызываемого клиентского метода.</span><span class="sxs-lookup"><span data-stu-id="c5af4-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="c5af4-174">Это оставляет код открытым для ошибок времени выполнения, если имя метода написано неправильно или отсутствует в клиенте.</span><span class="sxs-lookup"><span data-stu-id="c5af4-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="c5af4-175">Альтернативой использованию `SendAsync` является строгое типизирование `Hub` <xref:Microsoft.AspNetCore.SignalR.Hub%601> .</span><span class="sxs-lookup"><span data-stu-id="c5af4-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="c5af4-176">В следующем примере `ChatHub` клиентские методы были извлечены в интерфейс с именем `IChatClient` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="c5af4-177">Этот интерфейс можно использовать для реструктуризации предыдущего `ChatHub` примера.</span><span class="sxs-lookup"><span data-stu-id="c5af4-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="c5af4-178">Использование `Hub<IChatClient>` включает проверку клиентских методов во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="c5af4-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="c5af4-179">Это предотвращает проблемы, вызванные использованием волшебных строк, поскольку `Hub<T>` может предоставлять доступ только к методам, определенным в интерфейсе.</span><span class="sxs-lookup"><span data-stu-id="c5af4-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="c5af4-180">Использование строго типизированного `Hub<T>` ключа отключает возможность использования `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="c5af4-181">Все методы, определенные в интерфейсе, по-прежнему могут быть определены как асинхронные.</span><span class="sxs-lookup"><span data-stu-id="c5af4-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="c5af4-182">Фактически каждый из этих методов должен возвращать `Task` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="c5af4-183">Поскольку это интерфейс, не используйте `async` ключевое слово.</span><span class="sxs-lookup"><span data-stu-id="c5af4-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="c5af4-184">Пример:</span><span class="sxs-lookup"><span data-stu-id="c5af4-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="c5af4-185">`Async`Суффикс не удаляется из имени метода.</span><span class="sxs-lookup"><span data-stu-id="c5af4-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="c5af4-186">Если метод клиента не определен с помощью `.on('MyMethodAsync')` , не следует использовать `MyMethodAsync` в качестве имени.</span><span class="sxs-lookup"><span data-stu-id="c5af4-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="c5af4-187">Изменение имени метода концентратора</span><span class="sxs-lookup"><span data-stu-id="c5af4-187">Change the name of a hub method</span></span>

<span data-ttu-id="c5af4-188">По умолчанию имя метода концентратора сервера — это имя метода .NET.</span><span class="sxs-lookup"><span data-stu-id="c5af4-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="c5af4-189">Однако можно использовать атрибут [хубмесоднаме](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) , чтобы изменить это значение по умолчанию и вручную указать имя метода.</span><span class="sxs-lookup"><span data-stu-id="c5af4-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="c5af4-190">Клиент должен использовать это имя вместо имени метода .NET при вызове метода.</span><span class="sxs-lookup"><span data-stu-id="c5af4-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="c5af4-191">Обработку событий для подключения</span><span class="sxs-lookup"><span data-stu-id="c5af4-191">Handle events for a connection</span></span>

<span data-ttu-id="c5af4-192">SignalRAPI концентраторов предоставляет `OnConnectedAsync` `OnDisconnectedAsync` виртуальные методы и для управления подключениями и их мониторинга.</span><span class="sxs-lookup"><span data-stu-id="c5af4-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="c5af4-193">Переопределите `OnConnectedAsync` виртуальный метод, чтобы выполнять действия при подключении клиента к концентратору, например при его добавлении в группу.</span><span class="sxs-lookup"><span data-stu-id="c5af4-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="c5af4-194">Переопределите `OnDisconnectedAsync` виртуальный метод для выполнения действий при отключении клиента.</span><span class="sxs-lookup"><span data-stu-id="c5af4-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="c5af4-195">Если клиент намеренно отключается ( `connection.stop()` например, путем вызова), `exception` параметр будет иметь значение `null` .</span><span class="sxs-lookup"><span data-stu-id="c5af4-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="c5af4-196">Однако если Клиент отключен из-за ошибки (например, ошибки сети), `exception` параметр будет содержать исключение, описывающее ошибку.</span><span class="sxs-lookup"><span data-stu-id="c5af4-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="c5af4-197">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="c5af4-197">Handle errors</span></span>

<span data-ttu-id="c5af4-198">Исключения, вызываемые в методах концентратора, отправляются клиенту, вызвавшему метод.</span><span class="sxs-lookup"><span data-stu-id="c5af4-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="c5af4-199">На клиенте JavaScript `invoke` метод возвращает [обещание JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="c5af4-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="c5af4-200">Когда клиент получает сообщение об ошибке с обработчиком, присоединенным к обещанию с помощью `catch` , он вызывается и передается как `Error` объект JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c5af4-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="c5af4-201">Если концентратор создает исключение, соединения не закрываются.</span><span class="sxs-lookup"><span data-stu-id="c5af4-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="c5af4-202">По умолчанию SignalR возвращает клиенту общее сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="c5af4-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="c5af4-203">Пример:</span><span class="sxs-lookup"><span data-stu-id="c5af4-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="c5af4-204">Непредвиденные исключения часто содержат конфиденциальные сведения, такие как имя сервера базы данных в исключении, которое запускается при сбое подключения к базе данных.</span><span class="sxs-lookup"><span data-stu-id="c5af4-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="c5af4-205">SignalR не предоставляет эти подробные сообщения об ошибках по умолчанию в качестве меры безопасности.</span><span class="sxs-lookup"><span data-stu-id="c5af4-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="c5af4-206">Дополнительные сведения о том, почему сведения об исключении подавляются, см. в [статье вопросы безопасности](xref:signalr/security#exceptions) .</span><span class="sxs-lookup"><span data-stu-id="c5af4-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="c5af4-207">При наличии исключительных условий *, которые нужно* распространить на клиент, можно использовать `HubException` класс.</span><span class="sxs-lookup"><span data-stu-id="c5af4-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="c5af4-208">Если вы выдаете `HubException` из метода концентратора, SignalR  отправляет клиенту все сообщение без изменений.</span><span class="sxs-lookup"><span data-stu-id="c5af4-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="c5af4-209">SignalR отправляет клиенту только `Message` свойство исключения.</span><span class="sxs-lookup"><span data-stu-id="c5af4-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="c5af4-210">Трассировка стека и другие свойства исключения недоступны клиенту.</span><span class="sxs-lookup"><span data-stu-id="c5af4-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c5af4-211">Связанные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c5af4-211">Related resources</span></span>

* [<span data-ttu-id="c5af4-212">Введение в ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c5af4-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="c5af4-213">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="c5af4-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="c5af4-214">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="c5af4-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
