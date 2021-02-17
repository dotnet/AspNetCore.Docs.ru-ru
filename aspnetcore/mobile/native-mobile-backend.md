---
title: Создание внутренних служб для собственных мобильных приложений в ASP.NET Core
author: rick-anderson
description: Сведения о том, как создать внутренние службы с помощью MVC ASP.NET Core, чтобы обеспечить поддержку собственных мобильных приложений.
ms.author: riande
ms.date: 2/12/2021
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
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564030"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Создание внутренних служб для собственных мобильных приложений в ASP.NET Core

По [Джеймс монтеманьо](https://twitter.com/JamesMontemagno)

Мобильные приложения могут взаимодействовать с внутренними службами ASP.NET Core. Инструкции по подключению локальных веб-служб из симуляторов iOS и эмуляторов Android см. в статье о [подключении к локальным веб-службам из симуляторов iOS и эмуляторов Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Просмотреть или скачать пример кода внутренней службы](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>Пример собственного мобильного приложения

В этом руководстве показано, как создавать серверные службы с помощью ASP.NET Core для поддержки собственных мобильных приложений. Он использует [приложение Xamarin. Forms тодорест](/xamarin/xamarin-forms/data-cloud/consuming/rest) в качестве собственного клиента, включая отдельные клиенты машинного кода для Android, iOS и Windows. Вы можете следовать приложенному руководству, чтобы создать собственное приложение (и установить необходимые бесплатные средства Xamarin), а также скачать пример решения Xamarin. Пример Xamarin включает в себя проект служб веб-API ASP.NET Core, который заменяет приложение ASP.NET Core (без изменений, требуемых клиентом).

![Приложение To Do Rest, запущенное на смартфоне Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Компоненты

[Приложение тодорест](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) поддерживает перепись, добавление, удаление и обновление элементов To-Do. Каждый элемент имеет идентификатор, имя, заметки и свойство, указывающее, выполнен ли он.

Основное представление элементов, как показано выше, выводит список с именем каждого элемента, указывая, выполнен ли он, с помощью флажка.

Если выбрать значок `+`, открывается диалоговое окно добавления элемента:

![Диалоговое окно добавления элемента](native-mobile-backend/_static/todo-android-new-item.png)

При выборе элемента на экране основного списка открывается диалоговое окно редактирования, где можно изменить параметры имени, заметок и готовности элемента, а также удалить его:

![Диалоговое окно изменения элемента](native-mobile-backend/_static/todo-android-edit-item.png)

Чтобы протестировать себя на ASP.NET Core приложении, созданном в следующем разделе, работающем на компьютере, обновите [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) константу приложения.

Эмуляторы Android не работают на локальном компьютере и используют петлевой IP-адрес (10.0.2.2) для взаимодействия с локальным компьютером. Используйте [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) , чтобы определить, какая операционная система работает, чтобы использовать правильный URL-адрес.

Перейдите к [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) проекту и откройте [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) файл. Файл *Constants.CS* содержит следующую конфигурацию.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

При необходимости можно развернуть веб-службу в облачной службе, такой как Azure, и обновить `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>Создание проекта ASP.NET Core

Создайте веб-приложение ASP.NET Core в Visual Studio. Выберите шаблон веб-API. Назовите проект *TodoAPI*.

![Диалоговое окно создания веб-приложения ASP.NET с выбранным шаблоном проекта веб-API](native-mobile-backend/_static/web-api-template.png)

Приложение должно отвечать на все запросы к порту 5000, включая HTTP-трафик с открытым текстом для мобильного клиента. Обновите *Startup.CS* таким образом, чтобы <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> оно не запускалось в разработке:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> Запустите приложение напрямую, а не за IIS Express. По умолчанию IIS Express игнорирует нелокальные запросы. Выполните команду [DotNet Run](/dotnet/core/tools/dotnet-run) из командной строки или выберите профиль имени приложения в раскрывающемся списке цель отладки на панели инструментов Visual Studio.

Добавьте класс модели для представления элементов задач. Пометьте обязательные поля с помощью атрибута `[Required]`:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

Методам API нужен определенный способ для работы с данными. Используйте тот же интерфейс `ITodoRepository`, который использует исходный пример Xamarin:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

В этом примере реализация использует просто частную коллекцию элементов:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

Настройте реализацию в файле *Startup.cs*:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>Создание контроллера

Добавьте новый контроллер в проект [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs). Он должен наследовать от <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Добавьте атрибут `Route`, чтобы указать, что контроллер будет обрабатывать запросы, выполняемые по путям, начинающимся с `api/todoitems`. Токен `[controller]` в маршруте заменяется на имя контроллера (суффикс `Controller` опускается) и особенно удобен для глобальных маршрутов. Дополнительные сведения о [маршрутизации](../fundamentals/routing.md).

Для работы контроллеру нужен `ITodoRepository`; запросите экземпляр этого типа через конструктор контроллера. Во время выполнения этот экземпляр будет предоставляться с помощью поддержки [внедрения зависимостей](../fundamentals/dependency-injection.md) на платформе.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

Этот API поддерживает четыре различных HTTP-команды для выполнения операций CRUD (создание, чтение, обновление, удаление) с источником данных. Самой простой из них является операция чтения, которая соответствует HTTP-запросу GET.

### <a name="reading-items"></a>Чтение элементов

Запрос списка элементов, выполняется с помощью отправки запроса GET в метод `List`. Атрибут`[HttpGet]` в методе `List` указывает, что это действие должно обрабатывать только запросы GET. Маршрут для этого действия соответствует маршруту, указанному на контроллере. Использовать имя действия в составе маршрута необязательно. Нужно лишь убедиться, что каждое действие имеет уникальный и однозначный маршрут. Атрибуты маршрутизации можно применять на уровне как контроллера, так и метода для создания определенных маршрутов.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

`List`Метод возвращает код ответа ок 200 и все элементы TODO, сериализованные как JSON.

Вы можете проверить новый метод API с помощью различных средств, таких как [Postman](https://www.getpostman.com/docs/), как показано ниже:

![Консоль Postman, показывающая запрос GET для элементов задач и текст отклика, показывающий JSON для трех возвращенных элементов](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Создание элементов

По соглашению создание элементов данных сопоставляется с HTTP-командой POST. Метод `Create` имеет примененный к нему атрибут `[HttpPost]` и принимает экземпляр `TodoItem`. Так как аргумент `item` передается в текст POST, этот параметр указывает атрибут `[FromBody]`.

Внутри метода элемент проверяется на допустимость и предшествующее существование в хранилище данных, а при отсутствии проблем он добавляется с помощью репозитория. Проверка `ModelState.IsValid` приводит к [проверке модели](../mvc/models/validation.md) и должна выполняться в каждом методе API, принимающем вводимые пользователем данные.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

В примере используется `enum` код, содержащий коды ошибок, которые передаются мобильному клиенту:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

Проверьте добавление новых элементов с помощью Postman, выбрав команду POST, предоставляющую новый объект в формате JSON в тексте запроса. Вам также нужно добавить заголовок запроса, указав `Content-Type` типа `application/json`.

![Консоль Postman с командой POST и откликом](native-mobile-backend/_static/postman-post.png)

Метод возвращает вновь созданный элемент в отклике.

### <a name="updating-items"></a>Обновление элементов

Изменение записей осуществляется с помощью HTTP-запросов PUT. За исключением этого изменения, метод `Edit` практически идентичен `Create`. Обратите внимание, что если запись не найдена, то действие `Edit` возвратит отклик `NotFound` (404).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Чтобы выполнить проверку с помощью Postman, измените команду на PUT. Укажите обновленные данные объекта в тексте запроса.

![Консоль Postman с командой PUT и откликом](native-mobile-backend/_static/postman-put.png)

Этот метод возвращает отклик `NoContent` (204) при успешном выполнении, чтобы обеспечить согласованность с ранее существовавшими API.

### <a name="deleting-items"></a>Удаление элементов

Удаление записей сопровождается отправкой запросов DELETE в службу и передачей идентификатора удаляемого элемента. Как и в случае с обновлениями, запросы несуществующих элементов будут получать отклики `NotFound`. В противном случае успешный запрос получит отклик `NoContent` (204).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

Обратите внимание, что при тестировании функции удаления в тексте запроса не требуется никаких элементов.

![Консоль Postman с командой DELETE и откликом](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>Предотвращение избыточной публикации

В настоящее время пример приложения предоставляет весь объект `TodoItem`. Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели. Это связано с несколькими причинами, и безопасность является основной. Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления. В этой статье используется **DTO**.

DTO можно использовать для следующего:

* Предотвращение избыточной публикации.
* Скрытие свойств, которые не предназначены для просмотра клиентами.
* Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.
* Сведение графов объектов, содержащих вложенные объекты. Сведенные графы объектов могут быть удобнее для клиентов.

Чтобы продемонстрировать подход DTO, см. раздел [предотвращение избыточной отправки](xref:tutorials/first-web-api#prevent-over-posting) .

## <a name="common-web-api-conventions"></a>Общие соглашения веб-API

При разработке внутренних служб для своего приложения вам потребуется согласованный набор соглашений или политик для обработки сквозных задач. Например, в приведенной выше службе на запросы запрашивает определенные записи, которые не были найдены, и был получен отклик `NotFound`, а не `BadRequest`. Аналогичным образом, команды, выполненные для этой службы, которые передавались в привязанные типы модели, всегда проверяли `ModelState.IsValid` и возвращали `BadRequest` для недопустимых типов модели.

Определив общую политику для своих API, вы обычно можете инкапсулировать ее в [фильтр](../mvc/controllers/filters.md). Дополнительные сведения о том, [как инкапсулировать общие политики API в приложения ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Xamarin. Forms: проверка подлинности веб-службы](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: использование веб-службы RESTFUL](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: использование веб-служб RESTFUL в приложениях Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn. Создание веб-API с помощью ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
