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
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="4d01a-103">Создание внутренних служб для собственных мобильных приложений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d01a-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="4d01a-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="4d01a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4d01a-105">Мобильные приложения могут взаимодействовать с внутренними службами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4d01a-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="4d01a-106">Инструкции по подключению локальных веб-служб из симуляторов iOS и эмуляторов Android см. в статье о [подключении к локальным веб-службам из симуляторов iOS и эмуляторов Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="4d01a-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="4d01a-107">Просмотреть или скачать пример кода внутренней службы</span><span class="sxs-lookup"><span data-stu-id="4d01a-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="4d01a-108">Пример собственного мобильного приложения</span><span class="sxs-lookup"><span data-stu-id="4d01a-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="4d01a-109">Это руководство описывает, как создать внутренние службы с помощью ASP.NET Core MVC, чтобы обеспечить поддержку собственных мобильных приложений.</span><span class="sxs-lookup"><span data-stu-id="4d01a-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="4d01a-110">В нем [приложение Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) используется в качестве собственного клиента, который содержит отдельные собственные клиенты для устройств на базе Android, iOS, универсальной платформы Windows и Window Phone.</span><span class="sxs-lookup"><span data-stu-id="4d01a-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="4d01a-111">Вы можете следовать приложенному руководству, чтобы создать собственное приложение (и установить необходимые бесплатные средства Xamarin), а также скачать пример решения Xamarin.</span><span class="sxs-lookup"><span data-stu-id="4d01a-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="4d01a-112">Пример Xamarin включает в себя проект служб веб-API ASP.NET 2, заменяемый приведенным в этой статье приложением ASP.NET Core (со стороны клиента никаких изменений не требуется).</span><span class="sxs-lookup"><span data-stu-id="4d01a-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Приложение To Do Rest, запущенное на смартфоне Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="4d01a-114">Компоненты</span><span class="sxs-lookup"><span data-stu-id="4d01a-114">Features</span></span>

<span data-ttu-id="4d01a-115">Приложение ToDoRest поддерживает перечисление, добавление, удаление и обновление элементов задач.</span><span class="sxs-lookup"><span data-stu-id="4d01a-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="4d01a-116">Каждый элемент имеет идентификатор, имя, заметки и свойство, указывающее, выполнен ли он.</span><span class="sxs-lookup"><span data-stu-id="4d01a-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="4d01a-117">Основное представление элементов, как показано выше, выводит список с именем каждого элемента, указывая, выполнен ли он, с помощью флажка.</span><span class="sxs-lookup"><span data-stu-id="4d01a-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="4d01a-118">Если выбрать значок `+`, открывается диалоговое окно добавления элемента:</span><span class="sxs-lookup"><span data-stu-id="4d01a-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Диалоговое окно добавления элемента](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="4d01a-120">При выборе элемента на экране основного списка открывается диалоговое окно редактирования, где можно изменить параметры имени, заметок и готовности элемента, а также удалить его:</span><span class="sxs-lookup"><span data-stu-id="4d01a-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Диалоговое окно изменения элемента](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="4d01a-122">По умолчанию этот пример настроен для использования внутренних служб, размещенных на сайте developer.xamarin.com и допускающих операции только для чтения.</span><span class="sxs-lookup"><span data-stu-id="4d01a-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="4d01a-123">Чтобы самостоятельно протестировать его для сравнения с приложением ASP.NET Core, создаваемым в следующем разделе, на своем компьютере, нужно обновить константу `RestUrl` приложения.</span><span class="sxs-lookup"><span data-stu-id="4d01a-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="4d01a-124">Перейдите к проекту `ToDoREST` и откройте файл *Constants.cs*.</span><span class="sxs-lookup"><span data-stu-id="4d01a-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="4d01a-125">Замените `RestUrl` на URL-адрес, включающий IP-адрес вашего компьютера (не "localhost" или 127.0.0.1, так как этот адрес используется из эмулятора устройства, а не с вашего компьютера).</span><span class="sxs-lookup"><span data-stu-id="4d01a-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="4d01a-126">Включите также номер порта (5000).</span><span class="sxs-lookup"><span data-stu-id="4d01a-126">Include the port number as well (5000).</span></span> <span data-ttu-id="4d01a-127">Чтобы поверить работу служб с устройством, убедитесь, что в брандмауэре не активна блокировка доступа к этому порту.</span><span class="sxs-lookup"><span data-stu-id="4d01a-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="4d01a-128">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d01a-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="4d01a-129">Создайте веб-приложение ASP.NET Core в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4d01a-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="4d01a-130">Выберите шаблон "Веб-API" и параметр "Без проверки".</span><span class="sxs-lookup"><span data-stu-id="4d01a-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="4d01a-131">Назовите проект *ToDoApi*.</span><span class="sxs-lookup"><span data-stu-id="4d01a-131">Name the project *ToDoApi*.</span></span>

![Диалоговое окно создания веб-приложения ASP.NET с выбранным шаблоном проекта веб-API](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="4d01a-133">Приложение должно отвечать на все запросы, направляемые на порт 5000.</span><span class="sxs-lookup"><span data-stu-id="4d01a-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="4d01a-134">Для этого измените *Program.cs*, чтобы включить в него `.UseUrls("http://*:5000")`:</span><span class="sxs-lookup"><span data-stu-id="4d01a-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="4d01a-135">Обязательно запустите приложение напрямую, а не через IIS Express, так как это решение по умолчанию игнорирует нелокальные запросы.</span><span class="sxs-lookup"><span data-stu-id="4d01a-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="4d01a-136">Запустите [dotnet run](/dotnet/core/tools/dotnet-run) из командной строки или выберите профиль имени приложения в раскрывающемся списке "Целевой объект отладки" на панели инструментов Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="4d01a-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="4d01a-137">Добавьте класс модели для представления элементов задач.</span><span class="sxs-lookup"><span data-stu-id="4d01a-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="4d01a-138">Пометьте обязательные поля с помощью атрибута `[Required]`:</span><span class="sxs-lookup"><span data-stu-id="4d01a-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="4d01a-139">Методам API нужен определенный способ для работы с данными.</span><span class="sxs-lookup"><span data-stu-id="4d01a-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="4d01a-140">Используйте тот же интерфейс `IToDoRepository`, который использует исходный пример Xamarin:</span><span class="sxs-lookup"><span data-stu-id="4d01a-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="4d01a-141">В этом примере реализация использует просто частную коллекцию элементов:</span><span class="sxs-lookup"><span data-stu-id="4d01a-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="4d01a-142">Настройте реализацию в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4d01a-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="4d01a-143">На этом этапе вы готовы создать *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="4d01a-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="4d01a-144">Дополнительные сведения о создании веб-API см. в статье [Создание первого веб-API с помощью MVC ASP.NET Core и Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="4d01a-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="4d01a-145">Создание контроллера</span><span class="sxs-lookup"><span data-stu-id="4d01a-145">Creating the Controller</span></span>

<span data-ttu-id="4d01a-146">Добавьте в проект новый контроллер *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="4d01a-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="4d01a-147">Он должен наследовать от Microsoft.AspNetCore.Mvc.Controller.</span><span class="sxs-lookup"><span data-stu-id="4d01a-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="4d01a-148">Добавьте атрибут `Route`, чтобы указать, что контроллер будет обрабатывать запросы, выполняемые по путям, начинающимся с `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="4d01a-149">Токен `[controller]` в маршруте заменяется на имя контроллера (суффикс `Controller` опускается) и особенно удобен для глобальных маршрутов.</span><span class="sxs-lookup"><span data-stu-id="4d01a-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="4d01a-150">Дополнительные сведения о [маршрутизации](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="4d01a-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="4d01a-151">Для работы контроллеру нужен `IToDoRepository`; запросите экземпляр этого типа через конструктор контроллера.</span><span class="sxs-lookup"><span data-stu-id="4d01a-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="4d01a-152">Во время выполнения этот экземпляр будет предоставляться с помощью поддержки [внедрения зависимостей](../fundamentals/dependency-injection.md) на платформе.</span><span class="sxs-lookup"><span data-stu-id="4d01a-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="4d01a-153">Этот API поддерживает четыре различных HTTP-команды для выполнения операций CRUD (создание, чтение, обновление, удаление) с источником данных.</span><span class="sxs-lookup"><span data-stu-id="4d01a-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="4d01a-154">Самой простой из них является операция чтения, которая соответствует HTTP-запросу GET.</span><span class="sxs-lookup"><span data-stu-id="4d01a-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="4d01a-155">Чтение элементов</span><span class="sxs-lookup"><span data-stu-id="4d01a-155">Reading Items</span></span>

<span data-ttu-id="4d01a-156">Запрос списка элементов, выполняется с помощью отправки запроса GET в метод `List`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="4d01a-157">Атрибут`[HttpGet]` в методе `List` указывает, что это действие должно обрабатывать только запросы GET.</span><span class="sxs-lookup"><span data-stu-id="4d01a-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="4d01a-158">Маршрут для этого действия соответствует маршруту, указанному на контроллере.</span><span class="sxs-lookup"><span data-stu-id="4d01a-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="4d01a-159">Использовать имя действия в составе маршрута необязательно.</span><span class="sxs-lookup"><span data-stu-id="4d01a-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="4d01a-160">Нужно лишь убедиться, что каждое действие имеет уникальный и однозначный маршрут.</span><span class="sxs-lookup"><span data-stu-id="4d01a-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="4d01a-161">Атрибуты маршрутизации можно применять на уровне как контроллера, так и метода для создания определенных маршрутов.</span><span class="sxs-lookup"><span data-stu-id="4d01a-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="4d01a-162">Метод `List` возвращает код ответа "200 ОК" и все элементы задач, сериализованные в виде JSON.</span><span class="sxs-lookup"><span data-stu-id="4d01a-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="4d01a-163">Вы можете проверить новый метод API с помощью различных средств, таких как [Postman](https://www.getpostman.com/docs/), как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="4d01a-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Консоль Postman, показывающая запрос GET для элементов задач и текст отклика, показывающий JSON для трех возвращенных элементов](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="4d01a-165">Создание элементов</span><span class="sxs-lookup"><span data-stu-id="4d01a-165">Creating Items</span></span>

<span data-ttu-id="4d01a-166">По соглашению создание элементов данных сопоставляется с HTTP-командой POST.</span><span class="sxs-lookup"><span data-stu-id="4d01a-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="4d01a-167">Метод `Create` имеет примененный к нему атрибут `[HttpPost]` и принимает экземпляр `ToDoItem`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="4d01a-168">Так как аргумент `item` передается в текст POST, этот параметр указывает атрибут `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="4d01a-169">Внутри метода элемент проверяется на допустимость и предшествующее существование в хранилище данных, а при отсутствии проблем он добавляется с помощью репозитория.</span><span class="sxs-lookup"><span data-stu-id="4d01a-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="4d01a-170">Проверка `ModelState.IsValid` приводит к [проверке модели](../mvc/models/validation.md) и должна выполняться в каждом методе API, принимающем вводимые пользователем данные.</span><span class="sxs-lookup"><span data-stu-id="4d01a-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="4d01a-171">Этот пример использует перечисление, содержащее коды ошибок, которые передаются мобильному клиенту:</span><span class="sxs-lookup"><span data-stu-id="4d01a-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="4d01a-172">Проверьте добавление новых элементов с помощью Postman, выбрав команду POST, предоставляющую новый объект в формате JSON в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="4d01a-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="4d01a-173">Вам также нужно добавить заголовок запроса, указав `Content-Type` типа `application/json`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Консоль Postman с командой POST и откликом](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="4d01a-175">Метод возвращает вновь созданный элемент в отклике.</span><span class="sxs-lookup"><span data-stu-id="4d01a-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="4d01a-176">Обновление элементов</span><span class="sxs-lookup"><span data-stu-id="4d01a-176">Updating Items</span></span>

<span data-ttu-id="4d01a-177">Изменение записей осуществляется с помощью HTTP-запросов PUT.</span><span class="sxs-lookup"><span data-stu-id="4d01a-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="4d01a-178">За исключением этого изменения, метод `Edit` практически идентичен `Create`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="4d01a-179">Обратите внимание, что если запись не найдена, то действие `Edit` возвратит отклик `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="4d01a-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="4d01a-180">Чтобы выполнить проверку с помощью Postman, измените команду на PUT.</span><span class="sxs-lookup"><span data-stu-id="4d01a-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="4d01a-181">Укажите обновленные данные объекта в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="4d01a-181">Specify the updated object data in the Body of the request.</span></span>

![Консоль Postman с командой PUT и откликом](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="4d01a-183">Этот метод возвращает отклик `NoContent` (204) при успешном выполнении, чтобы обеспечить согласованность с ранее существовавшими API.</span><span class="sxs-lookup"><span data-stu-id="4d01a-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="4d01a-184">Удаление элементов</span><span class="sxs-lookup"><span data-stu-id="4d01a-184">Deleting Items</span></span>

<span data-ttu-id="4d01a-185">Удаление записей сопровождается отправкой запросов DELETE в службу и передачей идентификатора удаляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="4d01a-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="4d01a-186">Как и в случае с обновлениями, запросы несуществующих элементов будут получать отклики `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="4d01a-187">В противном случае успешный запрос получит отклик `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="4d01a-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="4d01a-188">Обратите внимание, что при тестировании функции удаления в тексте запроса не требуется никаких элементов.</span><span class="sxs-lookup"><span data-stu-id="4d01a-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Консоль Postman с командой DELETE и откликом](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="4d01a-190">Общие соглашения веб-API</span><span class="sxs-lookup"><span data-stu-id="4d01a-190">Common Web API Conventions</span></span>

<span data-ttu-id="4d01a-191">При разработке внутренних служб для своего приложения вам потребуется согласованный набор соглашений или политик для обработки сквозных задач.</span><span class="sxs-lookup"><span data-stu-id="4d01a-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="4d01a-192">Например, в приведенной выше службе на запросы запрашивает определенные записи, которые не были найдены, и был получен отклик `NotFound`, а не `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="4d01a-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="4d01a-193">Аналогичным образом, команды, выполненные для этой службы, которые передавались в привязанные типы модели, всегда проверяли `ModelState.IsValid` и возвращали `BadRequest` для недопустимых типов модели.</span><span class="sxs-lookup"><span data-stu-id="4d01a-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="4d01a-194">Определив общую политику для своих API, вы обычно можете инкапсулировать ее в [фильтр](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="4d01a-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="4d01a-195">Дополнительные сведения о том, [как инкапсулировать общие политики API в приложения ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="4d01a-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d01a-196">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4d01a-196">Additional resources</span></span>

- [<span data-ttu-id="4d01a-197">Xamarin. Forms: проверка подлинности веб-службы</span><span class="sxs-lookup"><span data-stu-id="4d01a-197">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="4d01a-198">Xamarin. Forms: использование веб-службы RESTFUL</span><span class="sxs-lookup"><span data-stu-id="4d01a-198">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="4d01a-199">Microsoft Learn: использование веб-служб RESTFUL в приложениях Xamarin</span><span class="sxs-lookup"><span data-stu-id="4d01a-199">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="4d01a-200">Microsoft Learn: создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d01a-200">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
