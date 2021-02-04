---
title: ASP.NET Core узла SignalR в фоновых службах
author: bradygaster
description: Узнайте, как отправить сообщения SignalR клиентам из классов .NET Core BackgroundService.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 4b877c64a881fec15ac9e9bd74ffdde0b5fa60f9
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530181"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>ASP.NET Core узла SignalR в фоновых службах

По [Брейди Гастер](https://twitter.com/bradygaster)

В этой статье приводятся рекомендации по следующим вопросам.

* Размещение SignalR концентраторов с помощью фонового рабочего процесса, размещенного в ASP.NET Core.
* Отправка сообщений на подключенные клиенты из [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).NET Core.

::: moniker range=">= aspnetcore-3.0"

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(описание загрузки)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(описание загрузки)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>Включить SignalR при запуске

::: moniker range=">= aspnetcore-3.0"

Размещение концентраторов ASP.NET Core SignalR в контексте фонового рабочего процесса идентично размещению центра в веб-приложении ASP.NET Core. В `Startup.ConfigureServices` методе вызывается `services.AddSignalR` Добавление требуемых служб в уровень поддержки ASP.NET Core внедрения зависимостей (DI) SignalR . В `Startup.Configure` `MapHub` метод вызывается в `UseEndpoints` обратном вызове для соединения конечных точек концентратора в конвейере запросов ASP.NET Core.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

Размещение концентраторов ASP.NET Core SignalR в контексте фонового рабочего процесса идентично размещению центра в веб-приложении ASP.NET Core. В `Startup.ConfigureServices` методе вызывается `services.AddSignalR` Добавление требуемых служб в уровень поддержки ASP.NET Core внедрения зависимостей (DI) SignalR . В `Startup.Configure` `UseSignalR` метод вызывается для подключения конечных точек концентратора в конвейере запросов ASP.NET Core.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

В предыдущем примере `ClockHub` класс реализует `Hub<T>` класс для создания строго типизированного концентратора. Объект `ClockHub` настроен в `Startup` классе для реагирования на запросы в конечной точке `/hubs/clock` .

Дополнительные сведения о строго типизированных концентраторах см. [в статье Использование концентраторов в SignalR для ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Эта функция не ограничена классом [Hub \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) . Любой класс, наследующий от [концентратора](xref:Microsoft.AspNetCore.SignalR.Hub), например [динамичуб](xref:Microsoft.AspNetCore.SignalR.DynamicHub), работает.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

Интерфейс, используемый строго типизированным объектом, `ClockHub` является `IClock` интерфейсом.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>Вызов SignalR концентратора из фоновой службы

Во время запуска `Worker` класс ( `BackgroundService` ) включается с помощью `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

Так как SignalR также включается на `Startup` этапе, в котором каждый концентратор подключен к отдельной конечной точке в КОНВЕЙЕРЕ HTTP-запросов ASP.NET Core, каждый концентратор представляется `IHubContext<T>` на сервере. Используя функции DI ASP.NET Core, другие классы, созданные на уровне размещения, такие как `BackgroundService` классы, классы контроллеров MVC или Razor модели страниц, могут получать ссылки на серверные концентраторы, принимая экземпляры `IHubContext<ClockHub, IClock>` во время создания.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

Так как `ExecuteAsync` метод вызывается итеративно в фоновой службе, текущая дата и время сервера отправляются подключенным клиентам с помощью `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>Реагирование на SignalR события с помощью фоновых служб

Как и в одностраничном приложении, использующем клиент JavaScript для SignalR , или классическое приложение .NET, использующее <xref:signalr/dotnet-client> , для `BackgroundService` `IHostedService` подключения к SignalR концентраторам и реагирования на события можно также использовать реализацию.

`ClockHubClient`Класс реализует `IClock` интерфейс и интерфейс `IHostedService` . Таким образом, его можно включить во время `Startup` непрерывного выполнения и реагирования на события концентратора с сервера.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Во время инициализации объект `ClockHubClient` создает экземпляр класса `HubConnection` и включает `IClock.ShowTime` метод в качестве обработчика для события концентратора `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

В `IHostedService.StartAsync` реализации `HubConnection` запускается асинхронно.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Во время выполнения `IHostedService.StopAsync` метода объект `HubConnection` уничтожается асинхронно.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

В `IHostedService.StartAsync` реализации `HubConnection` запускается асинхронно.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Во время выполнения `IHostedService.StopAsync` метода объект `HubConnection` уничтожается асинхронно.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Начало работы](xref:tutorials/signalr)
* [Концентраторы](xref:signalr/hubs)
* [Публикация в Azure](xref:signalr/publish-to-azure-web-app)
* [Строго типизированные концентраторы](xref:signalr/hubs#strongly-typed-hubs)
