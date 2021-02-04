---
title: Создание внутренних служб для собственных мобильных приложений в ASP.NET Core
author: ardalis
description: Сведения о том, как создать внутренние службы с помощью MVC ASP.NET Core, чтобы обеспечить поддержку собственных мобильных приложений.
ms.author: riande
ms.date: 12/05/2019
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530220"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Создание внутренних служб для собственных мобильных приложений в ASP.NET Core

Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)

Мобильные приложения могут взаимодействовать с внутренними службами ASP.NET Core. Инструкции по подключению локальных веб-служб из симуляторов iOS и эмуляторов Android см. в статье о [подключении к локальным веб-службам из симуляторов iOS и эмуляторов Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Просмотреть или скачать пример кода внутренней службы](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a>Пример собственного мобильного приложения

Это руководство описывает, как создать внутренние службы с помощью ASP.NET Core MVC, чтобы обеспечить поддержку собственных мобильных приложений. В нем [приложение Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) используется в качестве собственного клиента, который содержит отдельные собственные клиенты для устройств на базе Android, iOS, универсальной платформы Windows и Window Phone. Вы можете следовать приложенному руководству, чтобы создать собственное приложение (и установить необходимые бесплатные средства Xamarin), а также скачать пример решения Xamarin. Пример Xamarin включает в себя проект служб веб-API ASP.NET 2, заменяемый приведенным в этой статье приложением ASP.NET Core (со стороны клиента никаких изменений не требуется).

![Приложение To Do Rest, запущенное на смартфоне Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Компоненты

Приложение ToDoRest поддерживает перечисление, добавление, удаление и обновление элементов задач. Каждый элемент имеет идентификатор, имя, заметки и свойство, указывающее, выполнен ли он.

Основное представление элементов, как показано выше, выводит список с именем каждого элемента, указывая, выполнен ли он, с помощью флажка.

Если выбрать значок `+`, открывается диалоговое окно добавления элемента:

![Диалоговое окно добавления элемента](native-mobile-backend/_static/todo-android-new-item.png)

При выборе элемента на экране основного списка открывается диалоговое окно редактирования, где можно изменить параметры имени, заметок и готовности элемента, а также удалить его:

![Диалоговое окно изменения элемента](native-mobile-backend/_static/todo-android-edit-item.png)

По умолчанию этот пример настроен для использования внутренних служб, размещенных на сайте developer.xamarin.com и допускающих операции только для чтения. Чтобы самостоятельно протестировать его для сравнения с приложением ASP.NET Core, создаваемым в следующем разделе, на своем компьютере, нужно обновить константу `RestUrl` приложения. Перейдите к проекту `ToDoREST` и откройте файл *Constants.cs*. Замените `RestUrl` на URL-адрес, включающий IP-адрес вашего компьютера (не "localhost" или 127.0.0.1, так как этот адрес используется из эмулятора устройства, а не с вашего компьютера). Включите также номер порта (5000). Чтобы поверить работу служб с устройством, убедитесь, что в брандмауэре не активна блокировка доступа к этому порту.

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a>Создание проекта ASP.NET Core

Создайте веб-приложение ASP.NET Core в Visual Studio. Выберите шаблон "Веб-API" и параметр "Без проверки". Назовите проект *ToDoApi*.

![Диалоговое окно создания веб-приложения ASP.NET с выбранным шаблоном проекта веб-API](native-mobile-backend/_static/web-api-template.png)

Приложение должно отвечать на все запросы, направляемые на порт 5000. Для этого измените *Program.cs*, чтобы включить в него `.UseUrls("http://*:5000")`:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> Обязательно запустите приложение напрямую, а не через IIS Express, так как это решение по умолчанию игнорирует нелокальные запросы. Запустите [dotnet run](/dotnet/core/tools/dotnet-run) из командной строки или выберите профиль имени приложения в раскрывающемся списке "Целевой объект отладки" на панели инструментов Visual Studio.

Добавьте класс модели для представления элементов задач. Пометьте обязательные поля с помощью атрибута `[Required]`:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

Методам API нужен определенный способ для работы с данными. Используйте тот же интерфейс `IToDoRepository`, который использует исходный пример Xamarin:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

В этом примере реализация использует просто частную коллекцию элементов:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

Настройте реализацию в файле *Startup.cs*:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

На этом этапе вы готовы создать *ToDoItemsController*.

> [!TIP]
> Дополнительные сведения о создании веб-API см. в статье [Создание первого веб-API с помощью MVC ASP.NET Core и Visual Studio](../tutorials/first-web-api.md).

## <a name="creating-the-controller"></a>Создание контроллера

Добавьте в проект новый контроллер *ToDoItemsController*. Он должен наследовать от Microsoft.AspNetCore.Mvc.Controller. Добавьте атрибут `Route`, чтобы указать, что контроллер будет обрабатывать запросы, выполняемые по путям, начинающимся с `api/todoitems`. Токен `[controller]` в маршруте заменяется на имя контроллера (суффикс `Controller` опускается) и особенно удобен для глобальных маршрутов. Дополнительные сведения о [маршрутизации](../fundamentals/routing.md).

Для работы контроллеру нужен `IToDoRepository`; запросите экземпляр этого типа через конструктор контроллера. Во время выполнения этот экземпляр будет предоставляться с помощью поддержки [внедрения зависимостей](../fundamentals/dependency-injection.md) на платформе.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

Этот API поддерживает четыре различных HTTP-команды для выполнения операций CRUD (создание, чтение, обновление, удаление) с источником данных. Самой простой из них является операция чтения, которая соответствует HTTP-запросу GET.

### <a name="reading-items"></a>Чтение элементов

Запрос списка элементов, выполняется с помощью отправки запроса GET в метод `List`. Атрибут`[HttpGet]` в методе `List` указывает, что это действие должно обрабатывать только запросы GET. Маршрут для этого действия соответствует маршруту, указанному на контроллере. Использовать имя действия в составе маршрута необязательно. Нужно лишь убедиться, что каждое действие имеет уникальный и однозначный маршрут. Атрибуты маршрутизации можно применять на уровне как контроллера, так и метода для создания определенных маршрутов.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

Метод `List` возвращает код ответа "200 ОК" и все элементы задач, сериализованные в виде JSON.

Вы можете проверить новый метод API с помощью различных средств, таких как [Postman](https://www.getpostman.com/docs/), как показано ниже:

![Консоль Postman, показывающая запрос GET для элементов задач и текст отклика, показывающий JSON для трех возвращенных элементов](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Создание элементов

По соглашению создание элементов данных сопоставляется с HTTP-командой POST. Метод `Create` имеет примененный к нему атрибут `[HttpPost]` и принимает экземпляр `ToDoItem`. Так как аргумент `item` передается в текст POST, этот параметр указывает атрибут `[FromBody]`.

Внутри метода элемент проверяется на допустимость и предшествующее существование в хранилище данных, а при отсутствии проблем он добавляется с помощью репозитория. Проверка `ModelState.IsValid` приводит к [проверке модели](../mvc/models/validation.md) и должна выполняться в каждом методе API, принимающем вводимые пользователем данные.

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

Этот пример использует перечисление, содержащее коды ошибок, которые передаются мобильному клиенту:

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

Проверьте добавление новых элементов с помощью Postman, выбрав команду POST, предоставляющую новый объект в формате JSON в тексте запроса. Вам также нужно добавить заголовок запроса, указав `Content-Type` типа `application/json`.

![Консоль Postman с командой POST и откликом](native-mobile-backend/_static/postman-post.png)

Метод возвращает вновь созданный элемент в отклике.

### <a name="updating-items"></a>Обновление элементов

Изменение записей осуществляется с помощью HTTP-запросов PUT. За исключением этого изменения, метод `Edit` практически идентичен `Create`. Обратите внимание, что если запись не найдена, то действие `Edit` возвратит отклик `NotFound` (404).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

Чтобы выполнить проверку с помощью Postman, измените команду на PUT. Укажите обновленные данные объекта в тексте запроса.

![Консоль Postman с командой PUT и откликом](native-mobile-backend/_static/postman-put.png)

Этот метод возвращает отклик `NoContent` (204) при успешном выполнении, чтобы обеспечить согласованность с ранее существовавшими API.

### <a name="deleting-items"></a>Удаление элементов

Удаление записей сопровождается отправкой запросов DELETE в службу и передачей идентификатора удаляемого элемента. Как и в случае с обновлениями, запросы несуществующих элементов будут получать отклики `NotFound`. В противном случае успешный запрос получит отклик `NoContent` (204).

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

Обратите внимание, что при тестировании функции удаления в тексте запроса не требуется никаких элементов.

![Консоль Postman с командой DELETE и откликом](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a>Общие соглашения веб-API

При разработке внутренних служб для своего приложения вам потребуется согласованный набор соглашений или политик для обработки сквозных задач. Например, в приведенной выше службе на запросы запрашивает определенные записи, которые не были найдены, и был получен отклик `NotFound`, а не `BadRequest`. Аналогичным образом, команды, выполненные для этой службы, которые передавались в привязанные типы модели, всегда проверяли `ModelState.IsValid` и возвращали `BadRequest` для недопустимых типов модели.

Определив общую политику для своих API, вы обычно можете инкапсулировать ее в [фильтр](../mvc/controllers/filters.md). Дополнительные сведения о том, [как инкапсулировать общие политики API в приложения ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Xamarin. Forms: проверка подлинности веб-службы](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: использование веб-службы RESTFUL](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: использование веб-служб RESTFUL в приложениях Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn: создание веб-API с помощью ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
