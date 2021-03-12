---
title: ASP.NET Core SignalR клиент .NET
author: bradygaster
description: Сведения о SignalR клиенте ASP.NET Core .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: db2bfa6d3aa440615c5a9c17ae843dbe22755c97
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589248"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="831ea-103">ASP.NET Core SignalR клиент .NET</span><span class="sxs-lookup"><span data-stu-id="831ea-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="831ea-104">SignalRКлиентская библиотека ASP.NET Core .NET позволяет взаимодействовать с SignalR концентраторами из приложений .NET.</span><span class="sxs-lookup"><span data-stu-id="831ea-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="831ea-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/dotnet-client/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="831ea-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="831ea-106">Пример кода в этой статье — это приложение WPF, которое использует клиент ASP.NET Core SignalR .NET.</span><span class="sxs-lookup"><span data-stu-id="831ea-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="831ea-107">Установка SignalR клиентского пакета .NET</span><span class="sxs-lookup"><span data-stu-id="831ea-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="831ea-108">Элемент [Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Для подключения клиентов .NET к концентраторам требуется клиентский пакет SignalR .</span><span class="sxs-lookup"><span data-stu-id="831ea-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="831ea-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="831ea-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="831ea-110">Чтобы установить клиентскую библиотеку, в окне **консоли диспетчера пакетов** выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="831ea-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="831ea-111">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="831ea-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="831ea-112">Чтобы установить клиентскую библиотеку, выполните в командной оболочке следующую команду:</span><span class="sxs-lookup"><span data-stu-id="831ea-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="831ea-113">Подключение к концентратору</span><span class="sxs-lookup"><span data-stu-id="831ea-113">Connect to a hub</span></span>

<span data-ttu-id="831ea-114">Чтобы установить соединение, создайте `HubConnectionBuilder` вызов и `Build` .</span><span class="sxs-lookup"><span data-stu-id="831ea-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="831ea-115">При создании соединения можно настроить URL-адрес концентратора, протокол, тип транспорта, уровень ведения журнала, заголовки и другие параметры.</span><span class="sxs-lookup"><span data-stu-id="831ea-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="831ea-116">Настройте все необходимые параметры, вставив любой из `HubConnectionBuilder` методов в `Build` .</span><span class="sxs-lookup"><span data-stu-id="831ea-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="831ea-117">Запустите соединение с `StartAsync` .</span><span class="sxs-lookup"><span data-stu-id="831ea-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="831ea-118">Обработано потерянное подключение</span><span class="sxs-lookup"><span data-stu-id="831ea-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="831ea-119">Автоматическое повторное подключение</span><span class="sxs-lookup"><span data-stu-id="831ea-119">Automatically reconnect</span></span>

<span data-ttu-id="831ea-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Можно настроить для автоматического повторного подключения с помощью `WithAutomaticReconnect` метода в <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="831ea-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="831ea-121">По умолчанию оно не будет автоматически восстановлено.</span><span class="sxs-lookup"><span data-stu-id="831ea-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="831ea-122">Без каких бы то ни было параметров, `WithAutomaticReconnect()` настраивает клиент на ожидание 0, 2, 10 и 30 секунд соответственно, прежде чем пытаться выполнить каждую попытку повторного подключения, останавливая после четырех неудачных попыток.</span><span class="sxs-lookup"><span data-stu-id="831ea-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="831ea-123">Перед запуском всех попыток повторного подключения компонент `HubConnection` переходит в `HubConnectionState.Reconnecting` состояние и запускает `Reconnecting` событие.</span><span class="sxs-lookup"><span data-stu-id="831ea-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="831ea-124">Это дает возможность предупредить пользователей о потере подключения и отключении элементов пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="831ea-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="831ea-125">Неинтерактивные приложения могут запускать постановку в очередь или удалять сообщения.</span><span class="sxs-lookup"><span data-stu-id="831ea-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="831ea-126">Если клиент успешно повторно подключится в течение первых четырех попыток, компонент `HubConnection` вернется в `Connected` состояние и начнет `Reconnected` событие.</span><span class="sxs-lookup"><span data-stu-id="831ea-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="831ea-127">Это дает возможность информировать пользователей о том, что подключение было восстановлено, и выводит сообщения из очереди.</span><span class="sxs-lookup"><span data-stu-id="831ea-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="831ea-128">Так как соединение выглядит совершенно только для сервера, для `ConnectionId` `Reconnected` обработчиков событий будет предоставлен новый.</span><span class="sxs-lookup"><span data-stu-id="831ea-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="831ea-129">`Reconnected`Параметр обработчика событий `connectionId` будет иметь значение null, если `HubConnection` был настроен на [пропуск согласования](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="831ea-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="831ea-130">`WithAutomaticReconnect()` не настраивается `HubConnection` для повтора начальных сбоев запуска, поэтому ошибки запуска должны быть обработаны вручную:</span><span class="sxs-lookup"><span data-stu-id="831ea-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="831ea-131">Если клиент не будет успешно подключаться в течение первых четырех попыток, компонент `HubConnection` перейдет в `Disconnected` состояние и запустит <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> событие.</span><span class="sxs-lookup"><span data-stu-id="831ea-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="831ea-132">Это дает возможность попытаться вручную перезапустить подключение или сообщить пользователям, что подключение было безвозвратно утрачено.</span><span class="sxs-lookup"><span data-stu-id="831ea-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="831ea-133">Чтобы настроить настраиваемое число попыток повторного подключения перед отключением или изменением времени повторного подключения, `WithAutomaticReconnect` принимает массив чисел, представляющих задержку в миллисекундах, прежде чем начинать каждую попытку повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="831ea-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="831ea-134">В предыдущем примере настраивается `HubConnection` для запуска попытки повторного подключения сразу после потери соединения.</span><span class="sxs-lookup"><span data-stu-id="831ea-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="831ea-135">Это также справедливо для конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="831ea-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="831ea-136">Если первая попытка повторного подключения завершается неудачно, вторая попытка повторного подключения также начнется немедленно, а не подождать 2 секунды, как в конфигурации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="831ea-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="831ea-137">Если вторая попытка повторного подключения завершается неудачно, третья попытка повторного подключения начнется через 10 секунд, что опять же, как и конфигурация по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="831ea-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="831ea-138">Затем пользовательское поведение снова рассходится от поведения по умолчанию, останавливаясь после третьего сбоя попытки повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="831ea-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="831ea-139">В конфигурации по умолчанию будет предпринята еще одна повторная попытки подключения через 30 секунд.</span><span class="sxs-lookup"><span data-stu-id="831ea-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="831ea-140">Если требуется даже больший контроль над временем и числом попыток автоматического повторного подключения, `WithAutomaticReconnect` принимает объект, реализующий `IRetryPolicy` интерфейс, который имеет единственный метод с именем `NextRetryDelay` .</span><span class="sxs-lookup"><span data-stu-id="831ea-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="831ea-141">`NextRetryDelay` принимает один аргумент с типом `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="831ea-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="831ea-142">`RetryContext`Имеет три свойства: `PreviousRetryCount` , `ElapsedTime` и `RetryReason` , которые являются, а `long` `TimeSpan` и `Exception` соответственно.</span><span class="sxs-lookup"><span data-stu-id="831ea-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="831ea-143">Перед первой попыткой повторного подключения обе `PreviousRetryCount` и `ElapsedTime` будут равны нулю, и `RetryReason` будет исключено, что привело к потере соединения.</span><span class="sxs-lookup"><span data-stu-id="831ea-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="831ea-144">После каждой неудачной попытки повтора `PreviousRetryCount` значение будет увеличено на единицу, `ElapsedTime` будет обновлено с учетом времени, затраченного на повторное подключение, и `RetryReason` будет исключено, что привело к сбою последней попытки повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="831ea-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="831ea-145">`NextRetryDelay` должен возвращать значение TimeSpan, представляющее время ожидания перед следующей попыткой повторного подключения или `null` `HubConnection` отмене повторного подключения.</span><span class="sxs-lookup"><span data-stu-id="831ea-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="831ea-146">Кроме того, можно написать код, который будет повторно подключаться к клиенту вручную, как показано в [подсоединении вручную](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="831ea-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="831ea-147">Повторное подключение вручную</span><span class="sxs-lookup"><span data-stu-id="831ea-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="831ea-148">До 3,0 клиент .NET для SignalR не выполняет автоматическое повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="831ea-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="831ea-149">Необходимо написать код, который будет повторно подключаться к клиенту вручную.</span><span class="sxs-lookup"><span data-stu-id="831ea-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="831ea-150">Используйте <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> событие для реагирования на потерянное подключение.</span><span class="sxs-lookup"><span data-stu-id="831ea-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="831ea-151">Например, может потребоваться автоматизировать повторное подключение.</span><span class="sxs-lookup"><span data-stu-id="831ea-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="831ea-152">`Closed`Для этого события требуется делегат, возвращающий `Task` , что позволяет асинхронному коду выполняться без использования `async void` .</span><span class="sxs-lookup"><span data-stu-id="831ea-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="831ea-153">Чтобы удовлетворить сигнатуру делегата в `Closed` обработчике событий, который выполняется синхронно, возвращается `Task.CompletedTask` :</span><span class="sxs-lookup"><span data-stu-id="831ea-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="831ea-154">Основная причина поддержки асинхронности заключается в том, что вы можете перезапустить подключение.</span><span class="sxs-lookup"><span data-stu-id="831ea-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="831ea-155">Запуск соединения является асинхронным действием.</span><span class="sxs-lookup"><span data-stu-id="831ea-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="831ea-156">В `Closed` обработчике, который перезапускает подключение, рассмотрите возможность ожидания некоторой случайной задержки, чтобы предотвратить перегрузку сервера, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="831ea-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="831ea-157">Методы концентратора вызовов от клиента</span><span class="sxs-lookup"><span data-stu-id="831ea-157">Call hub methods from client</span></span>

<span data-ttu-id="831ea-158">`InvokeAsync` вызывает методы в концентраторе.</span><span class="sxs-lookup"><span data-stu-id="831ea-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="831ea-159">Передайте имя метода концентратора и все аргументы, определенные в методе концентратора, в `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="831ea-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="831ea-160">SignalR является асинхронным, поэтому используйте `async` и `await` при выполнении вызовов.</span><span class="sxs-lookup"><span data-stu-id="831ea-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="831ea-161">`InvokeAsync`Метод возвращает, `Task` который завершается, когда метод сервера возвращает значение.</span><span class="sxs-lookup"><span data-stu-id="831ea-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="831ea-162">Возвращаемое значение, если таковое имеется, предоставляется в качестве результата `Task` .</span><span class="sxs-lookup"><span data-stu-id="831ea-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="831ea-163">Все исключения, вызванные методом на сервере, вызывают сбой `Task` .</span><span class="sxs-lookup"><span data-stu-id="831ea-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="831ea-164">Используйте `await` синтаксис, чтобы дождаться завершения метода сервера и `try...catch` синтаксиса для обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="831ea-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="831ea-165">`SendAsync`Метод возвращает, `Task` который завершается, когда сообщение было отправлено на сервер.</span><span class="sxs-lookup"><span data-stu-id="831ea-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="831ea-166">Возвращаемое значение не предоставляется, так как это `Task` не ждет завершения метода сервера.</span><span class="sxs-lookup"><span data-stu-id="831ea-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="831ea-167">Все исключения, возникающие на клиенте при отправке сообщения, вызывают сбой `Task` .</span><span class="sxs-lookup"><span data-stu-id="831ea-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="831ea-168">Использование `await` `try...catch` синтаксиса и для управления ошибками отправки.</span><span class="sxs-lookup"><span data-stu-id="831ea-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="831ea-169">Вызов методов концентратора из клиента поддерживается только при использовании SignalR службы Azure в режиме *по умолчанию* .</span><span class="sxs-lookup"><span data-stu-id="831ea-169">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="831ea-170">Дополнительные сведения см. в разделе [часто задаваемые вопросы (репозиторий GitHub Azure SignalR)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="831ea-170">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="831ea-171">Вызов методов клиента из концентратора</span><span class="sxs-lookup"><span data-stu-id="831ea-171">Call client methods from hub</span></span>

<span data-ttu-id="831ea-172">Определите методы, которые концентратор вызывает с помощью `connection.On` после сборки, но перед запуском соединения.</span><span class="sxs-lookup"><span data-stu-id="831ea-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="831ea-173">Приведенный выше код `connection.On` выполняется, когда код на стороне сервера вызывает его с помощью `SendAsync` метода.</span><span class="sxs-lookup"><span data-stu-id="831ea-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="831ea-174">Обработка ошибок и ведение журнала</span><span class="sxs-lookup"><span data-stu-id="831ea-174">Error handling and logging</span></span>

<span data-ttu-id="831ea-175">Обрабатывайте ошибки с помощью оператора try-catch.</span><span class="sxs-lookup"><span data-stu-id="831ea-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="831ea-176">Проверьте `Exception` объект, чтобы определить необходимые действия после возникновения ошибки.</span><span class="sxs-lookup"><span data-stu-id="831ea-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="831ea-177">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="831ea-177">Additional resources</span></span>

* [<span data-ttu-id="831ea-178">Концентраторы</span><span class="sxs-lookup"><span data-stu-id="831ea-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="831ea-179">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="831ea-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="831ea-180">Публикация в Azure</span><span class="sxs-lookup"><span data-stu-id="831ea-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="831ea-181">SignalRДокументация, посвященная бессерверной службе Azure</span><span class="sxs-lookup"><span data-stu-id="831ea-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
