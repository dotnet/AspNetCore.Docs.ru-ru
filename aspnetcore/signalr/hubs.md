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
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Использование концентраторов в SignalR для ASP.NET Core

[Рейчел Аппель (](https://twitter.com/rachelappel) и [Кевин Гриффин](https://twitter.com/1kevgriff)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [(описание загрузки)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>Что такое SignalR центр

SignalRAPI концентраторов позволяет вызывать методы на подключенных клиентах с сервера. В серверном коде определяются методы, вызываемые клиентом. В клиентском коде определяются методы, которые вызываются с сервера. SignalR позаботится обо всем, за сценой, которая делает возможными обмен данными между клиентом и сервером в реальном времени.

## <a name="configure-signalr-hubs"></a>Настройка SignalR концентраторов

По SignalR промежуточного слоя требуются некоторые службы, которые настраиваются путем вызова `services.AddSignalR` .

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

При добавлении SignalR функциональных возможностей в ASP.NET Core приложение настройте SignalR маршруты путем вызова `endpoint.MapHub` в `Startup.Configure` `app.UseEndpoints` обратном вызове метода.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

При добавлении SignalR функциональных возможностей в ASP.NET Core приложение настройте SignalR маршруты путем вызова `app.UseSignalR` `Startup.Configure` метода в методе.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Создание и использование концентраторов

Создайте центр, объявив класс, наследующий от `Hub` , и добавьте в него открытые методы. Клиенты могут вызывать методы, определенные как `public` .

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Можно указать тип возвращаемого значения и параметры, включая сложные типы и массивы, как в любом методе C#. SignalR обрабатывает сериализацию и десериализацию сложных объектов и массивов в параметрах и возвращаемых значениях.

> [!NOTE]
> Концентраторы являются временными:
>
> * Не сохраняйте состояние в свойстве класса Hub. Каждый вызов метода концентратора выполняется в новом экземпляре концентратора.
> * Используется `await` при вызове асинхронных методов, зависящих от концентратора. Например, метод `Clients.All.SendAsync(...)` может завершиться ошибкой, если он вызывается без `await` , а метод концентратора завершается до `SendAsync` завершения.

## <a name="the-context-object"></a>Контекстный объект

`Hub`Класс имеет `Context` свойство, которое содержит следующие свойства со сведениями о соединении:

| Свойство | Описание |
| ------ | ----------- |
| `ConnectionId` | Возвращает уникальный идентификатор соединения, присвоенный SignalR . Для каждого соединения существует один идентификатор подключения.|
| `UserIdentifier` | Возвращает [идентификатор пользователя](xref:signalr/groups). По умолчанию SignalR использует `ClaimTypes.NameIdentifier` из из, `ClaimsPrincipal` связанного с соединением, в качестве идентификатора пользователя. |
| `User` | Возвращает объект, `ClaimsPrincipal` связанный с текущим пользователем. |
| `Items` | Возвращает коллекцию "ключ-значение", которую можно использовать для совместного использования данных в области этого соединения. Данные могут храниться в этой коллекции, и они будут сохранены для подключения через разные вызовы метода концентратора. |
| `Features` | Возвращает коллекцию функций, доступных в соединении. Сейчас эта коллекция не требуется в большинстве сценариев, поэтому она еще не документирована. |
| `ConnectionAborted` | Возвращает объект `CancellationToken` , уведомляющий о прерывании соединения. |

`Hub.Context` также содержит следующие методы.

| Метод | Описание |
| ------ | ----------- |
| `GetHttpContext` | Возвращает `HttpContext` для соединения или, `null` Если соединение не СВЯЗАНО с HTTP-запросом. Для HTTP-соединений этот метод можно использовать для получения таких сведений, как заголовки HTTP и строки запросов. |
| `Abort` | Прерывает подключение. |

## <a name="the-clients-object"></a>Объект «клиенты»

`Hub`Класс имеет `Clients` свойство, которое содержит следующие свойства для обмена данными между сервером и клиентом:

| Свойство | Описание |
| ------ | ----------- |
| `All` | Вызывает метод для всех подключенных клиентов |
| `Caller` | Вызывает метод для клиента, который вызвал метод концентратора |
| `Others` | Вызывает метод для всех подключенных клиентов, за исключением клиента, который вызвал метод |

`Hub.Clients` также содержит следующие методы.

| Метод | Описание |
| ------ | ----------- |
| `AllExcept` | Вызывает метод для всех подключенных клиентов, за исключением указанных соединений |
| `Client` | Вызывает метод для указанного подключенного клиента |
| `Clients` | Вызывает метод для конкретных подключенных клиентов |
| `Group` | Вызывает метод для всех соединений в указанной группе  |
| `GroupExcept` | Вызывает метод для всех соединений в указанной группе, за исключением указанных соединений |
| `Groups` | Вызывает метод для нескольких групп соединений  |
| `OthersInGroup` | Вызывает метод для группы соединений, исключая клиента, вызвавшего метод концентратора.  |
| `User` | Вызывает метод для всех соединений, связанных с конкретным пользователем |
| `Users` | Вызывает метод для всех подключений, связанных с указанными пользователями |

Каждое свойство или метод в приведенных выше таблицах возвращает объект с `SendAsync` методом. `SendAsync`Метод позволяет указать имя и параметры вызываемого клиентского метода.

## <a name="send-messages-to-clients"></a>Отправка сообщений клиентам

Чтобы вызвать определенные клиенты, используйте свойства `Clients` объекта. В следующем примере есть три метода концентратора:

* `SendMessage` отправляет сообщение всем подключенным клиентам с помощью `Clients.All` .
* `SendMessageToCaller` отправляет сообщение обратно вызывающему объекту с помощью `Clients.Caller` .
* `SendMessageToGroups` отправляет сообщение всем клиентам в `SignalR Users` группе.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Строго типизированные концентраторы

Недостаток использования заключается в `SendAsync` том, что он использует волшебную строку для указания вызываемого клиентского метода. Это оставляет код открытым для ошибок времени выполнения, если имя метода написано неправильно или отсутствует в клиенте.

Альтернативой использованию `SendAsync` является строгое типизирование `Hub` <xref:Microsoft.AspNetCore.SignalR.Hub%601> . В следующем примере `ChatHub` клиентские методы были извлечены в интерфейс с именем `IChatClient` .

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Этот интерфейс можно использовать для реструктуризации предыдущего `ChatHub` примера.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Использование `Hub<IChatClient>` включает проверку клиентских методов во время компиляции. Это предотвращает проблемы, вызванные использованием волшебных строк, поскольку `Hub<T>` может предоставлять доступ только к методам, определенным в интерфейсе.

Использование строго типизированного `Hub<T>` ключа отключает возможность использования `SendAsync` . Все методы, определенные в интерфейсе, по-прежнему могут быть определены как асинхронные. Фактически каждый из этих методов должен возвращать `Task` . Поскольку это интерфейс, не используйте `async` ключевое слово. Пример:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async`Суффикс не удаляется из имени метода. Если метод клиента не определен с помощью `.on('MyMethodAsync')` , не следует использовать `MyMethodAsync` в качестве имени.

## <a name="change-the-name-of-a-hub-method"></a>Изменение имени метода концентратора

По умолчанию имя метода концентратора сервера — это имя метода .NET. Однако можно использовать атрибут [хубмесоднаме](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) , чтобы изменить это значение по умолчанию и вручную указать имя метода. Клиент должен использовать это имя вместо имени метода .NET при вызове метода.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Обработку событий для подключения

SignalRAPI концентраторов предоставляет `OnConnectedAsync` `OnDisconnectedAsync` виртуальные методы и для управления подключениями и их мониторинга. Переопределите `OnConnectedAsync` виртуальный метод, чтобы выполнять действия при подключении клиента к концентратору, например при его добавлении в группу.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Переопределите `OnDisconnectedAsync` виртуальный метод для выполнения действий при отключении клиента. Если клиент намеренно отключается ( `connection.stop()` например, путем вызова), `exception` параметр будет иметь значение `null` . Однако если Клиент отключен из-за ошибки (например, ошибки сети), `exception` параметр будет содержать исключение, описывающее ошибку.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Обработка ошибок

Исключения, вызываемые в методах концентратора, отправляются клиенту, вызвавшему метод. На клиенте JavaScript `invoke` метод возвращает [обещание JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Когда клиент получает сообщение об ошибке с обработчиком, присоединенным к обещанию с помощью `catch` , он вызывается и передается как `Error` объект JavaScript.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Если концентратор создает исключение, соединения не закрываются. По умолчанию SignalR возвращает клиенту общее сообщение об ошибке. Пример:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Непредвиденные исключения часто содержат конфиденциальные сведения, такие как имя сервера базы данных в исключении, которое запускается при сбое подключения к базе данных. SignalR не предоставляет эти подробные сообщения об ошибках по умолчанию в качестве меры безопасности. Дополнительные сведения о том, почему сведения об исключении подавляются, см. в [статье вопросы безопасности](xref:signalr/security#exceptions) .

При наличии исключительных условий *, которые нужно* распространить на клиент, можно использовать `HubException` класс. Если вы выдаете `HubException` из метода концентратора, SignalR  отправляет клиенту все сообщение без изменений.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR отправляет клиенту только `Message` свойство исключения. Трассировка стека и другие свойства исключения недоступны клиенту.

## <a name="related-resources"></a>Связанные ресурсы

* [Введение в ASP.NET Core SignalR](xref:signalr/introduction)
* [Клиент JavaScript](xref:signalr/javascript-client)
* [Публикация в Azure](xref:signalr/publish-to-azure-web-app)
