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
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="52309-103">Создание внутренних служб для собственных мобильных приложений в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52309-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="52309-104">По [Джеймс монтеманьо](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="52309-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="52309-105">Мобильные приложения могут взаимодействовать с внутренними службами ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52309-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="52309-106">Инструкции по подключению локальных веб-служб из симуляторов iOS и эмуляторов Android см. в статье о [подключении к локальным веб-службам из симуляторов iOS и эмуляторов Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="52309-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="52309-107">Просмотреть или скачать пример кода внутренней службы</span><span class="sxs-lookup"><span data-stu-id="52309-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="52309-108">Пример собственного мобильного приложения</span><span class="sxs-lookup"><span data-stu-id="52309-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="52309-109">В этом руководстве показано, как создавать серверные службы с помощью ASP.NET Core для поддержки собственных мобильных приложений.</span><span class="sxs-lookup"><span data-stu-id="52309-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="52309-110">Он использует [приложение Xamarin. Forms тодорест](/xamarin/xamarin-forms/data-cloud/consuming/rest) в качестве собственного клиента, включая отдельные клиенты машинного кода для Android, iOS и Windows.</span><span class="sxs-lookup"><span data-stu-id="52309-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="52309-111">Вы можете следовать приложенному руководству, чтобы создать собственное приложение (и установить необходимые бесплатные средства Xamarin), а также скачать пример решения Xamarin.</span><span class="sxs-lookup"><span data-stu-id="52309-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="52309-112">Пример Xamarin включает в себя проект служб веб-API ASP.NET Core, который заменяет приложение ASP.NET Core (без изменений, требуемых клиентом).</span><span class="sxs-lookup"><span data-stu-id="52309-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Приложение To Do Rest, запущенное на смартфоне Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="52309-114">Компоненты</span><span class="sxs-lookup"><span data-stu-id="52309-114">Features</span></span>

<span data-ttu-id="52309-115">[Приложение тодорест](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) поддерживает перепись, добавление, удаление и обновление элементов To-Do.</span><span class="sxs-lookup"><span data-stu-id="52309-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="52309-116">Каждый элемент имеет идентификатор, имя, заметки и свойство, указывающее, выполнен ли он.</span><span class="sxs-lookup"><span data-stu-id="52309-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="52309-117">Основное представление элементов, как показано выше, выводит список с именем каждого элемента, указывая, выполнен ли он, с помощью флажка.</span><span class="sxs-lookup"><span data-stu-id="52309-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="52309-118">Если выбрать значок `+`, открывается диалоговое окно добавления элемента:</span><span class="sxs-lookup"><span data-stu-id="52309-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Диалоговое окно добавления элемента](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="52309-120">При выборе элемента на экране основного списка открывается диалоговое окно редактирования, где можно изменить параметры имени, заметок и готовности элемента, а также удалить его:</span><span class="sxs-lookup"><span data-stu-id="52309-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Диалоговое окно изменения элемента](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="52309-122">Чтобы протестировать себя на ASP.NET Core приложении, созданном в следующем разделе, работающем на компьютере, обновите [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) константу приложения.</span><span class="sxs-lookup"><span data-stu-id="52309-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="52309-123">Эмуляторы Android не работают на локальном компьютере и используют петлевой IP-адрес (10.0.2.2) для взаимодействия с локальным компьютером.</span><span class="sxs-lookup"><span data-stu-id="52309-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="52309-124">Используйте [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) , чтобы определить, какая операционная система работает, чтобы использовать правильный URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="52309-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="52309-125">Перейдите к [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) проекту и откройте [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) файл.</span><span class="sxs-lookup"><span data-stu-id="52309-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="52309-126">Файл *Constants.CS* содержит следующую конфигурацию.</span><span class="sxs-lookup"><span data-stu-id="52309-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="52309-127">При необходимости можно развернуть веб-службу в облачной службе, такой как Azure, и обновить `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="52309-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="52309-128">Создание проекта ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52309-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="52309-129">Создайте веб-приложение ASP.NET Core в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="52309-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="52309-130">Выберите шаблон веб-API.</span><span class="sxs-lookup"><span data-stu-id="52309-130">Choose the Web API template.</span></span> <span data-ttu-id="52309-131">Назовите проект *TodoAPI*.</span><span class="sxs-lookup"><span data-stu-id="52309-131">Name the project *TodoAPI*.</span></span>

![Диалоговое окно создания веб-приложения ASP.NET с выбранным шаблоном проекта веб-API](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="52309-133">Приложение должно отвечать на все запросы к порту 5000, включая HTTP-трафик с открытым текстом для мобильного клиента.</span><span class="sxs-lookup"><span data-stu-id="52309-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="52309-134">Обновите *Startup.CS* таким образом, чтобы <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> оно не запускалось в разработке:</span><span class="sxs-lookup"><span data-stu-id="52309-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="52309-135">Запустите приложение напрямую, а не за IIS Express.</span><span class="sxs-lookup"><span data-stu-id="52309-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="52309-136">По умолчанию IIS Express игнорирует нелокальные запросы.</span><span class="sxs-lookup"><span data-stu-id="52309-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="52309-137">Выполните команду [DotNet Run](/dotnet/core/tools/dotnet-run) из командной строки или выберите профиль имени приложения в раскрывающемся списке цель отладки на панели инструментов Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="52309-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="52309-138">Добавьте класс модели для представления элементов задач.</span><span class="sxs-lookup"><span data-stu-id="52309-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="52309-139">Пометьте обязательные поля с помощью атрибута `[Required]`:</span><span class="sxs-lookup"><span data-stu-id="52309-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="52309-140">Методам API нужен определенный способ для работы с данными.</span><span class="sxs-lookup"><span data-stu-id="52309-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="52309-141">Используйте тот же интерфейс `ITodoRepository`, который использует исходный пример Xamarin:</span><span class="sxs-lookup"><span data-stu-id="52309-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="52309-142">В этом примере реализация использует просто частную коллекцию элементов:</span><span class="sxs-lookup"><span data-stu-id="52309-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="52309-143">Настройте реализацию в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="52309-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="52309-144">Создание контроллера</span><span class="sxs-lookup"><span data-stu-id="52309-144">Creating the Controller</span></span>

<span data-ttu-id="52309-145">Добавьте новый контроллер в проект [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span><span class="sxs-lookup"><span data-stu-id="52309-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="52309-146">Он должен наследовать от <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="52309-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="52309-147">Добавьте атрибут `Route`, чтобы указать, что контроллер будет обрабатывать запросы, выполняемые по путям, начинающимся с `api/todoitems`.</span><span class="sxs-lookup"><span data-stu-id="52309-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="52309-148">Токен `[controller]` в маршруте заменяется на имя контроллера (суффикс `Controller` опускается) и особенно удобен для глобальных маршрутов.</span><span class="sxs-lookup"><span data-stu-id="52309-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="52309-149">Дополнительные сведения о [маршрутизации](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="52309-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="52309-150">Для работы контроллеру нужен `ITodoRepository`; запросите экземпляр этого типа через конструктор контроллера.</span><span class="sxs-lookup"><span data-stu-id="52309-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="52309-151">Во время выполнения этот экземпляр будет предоставляться с помощью поддержки [внедрения зависимостей](../fundamentals/dependency-injection.md) на платформе.</span><span class="sxs-lookup"><span data-stu-id="52309-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="52309-152">Этот API поддерживает четыре различных HTTP-команды для выполнения операций CRUD (создание, чтение, обновление, удаление) с источником данных.</span><span class="sxs-lookup"><span data-stu-id="52309-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="52309-153">Самой простой из них является операция чтения, которая соответствует HTTP-запросу GET.</span><span class="sxs-lookup"><span data-stu-id="52309-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="52309-154">Чтение элементов</span><span class="sxs-lookup"><span data-stu-id="52309-154">Reading Items</span></span>

<span data-ttu-id="52309-155">Запрос списка элементов, выполняется с помощью отправки запроса GET в метод `List`.</span><span class="sxs-lookup"><span data-stu-id="52309-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="52309-156">Атрибут`[HttpGet]` в методе `List` указывает, что это действие должно обрабатывать только запросы GET.</span><span class="sxs-lookup"><span data-stu-id="52309-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="52309-157">Маршрут для этого действия соответствует маршруту, указанному на контроллере.</span><span class="sxs-lookup"><span data-stu-id="52309-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="52309-158">Использовать имя действия в составе маршрута необязательно.</span><span class="sxs-lookup"><span data-stu-id="52309-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="52309-159">Нужно лишь убедиться, что каждое действие имеет уникальный и однозначный маршрут.</span><span class="sxs-lookup"><span data-stu-id="52309-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="52309-160">Атрибуты маршрутизации можно применять на уровне как контроллера, так и метода для создания определенных маршрутов.</span><span class="sxs-lookup"><span data-stu-id="52309-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="52309-161">`List`Метод возвращает код ответа ок 200 и все элементы TODO, сериализованные как JSON.</span><span class="sxs-lookup"><span data-stu-id="52309-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="52309-162">Вы можете проверить новый метод API с помощью различных средств, таких как [Postman](https://www.getpostman.com/docs/), как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="52309-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Консоль Postman, показывающая запрос GET для элементов задач и текст отклика, показывающий JSON для трех возвращенных элементов](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="52309-164">Создание элементов</span><span class="sxs-lookup"><span data-stu-id="52309-164">Creating Items</span></span>

<span data-ttu-id="52309-165">По соглашению создание элементов данных сопоставляется с HTTP-командой POST.</span><span class="sxs-lookup"><span data-stu-id="52309-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="52309-166">Метод `Create` имеет примененный к нему атрибут `[HttpPost]` и принимает экземпляр `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="52309-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="52309-167">Так как аргумент `item` передается в текст POST, этот параметр указывает атрибут `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="52309-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="52309-168">Внутри метода элемент проверяется на допустимость и предшествующее существование в хранилище данных, а при отсутствии проблем он добавляется с помощью репозитория.</span><span class="sxs-lookup"><span data-stu-id="52309-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="52309-169">Проверка `ModelState.IsValid` приводит к [проверке модели](../mvc/models/validation.md) и должна выполняться в каждом методе API, принимающем вводимые пользователем данные.</span><span class="sxs-lookup"><span data-stu-id="52309-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="52309-170">В примере используется `enum` код, содержащий коды ошибок, которые передаются мобильному клиенту:</span><span class="sxs-lookup"><span data-stu-id="52309-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="52309-171">Проверьте добавление новых элементов с помощью Postman, выбрав команду POST, предоставляющую новый объект в формате JSON в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="52309-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="52309-172">Вам также нужно добавить заголовок запроса, указав `Content-Type` типа `application/json`.</span><span class="sxs-lookup"><span data-stu-id="52309-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Консоль Postman с командой POST и откликом](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="52309-174">Метод возвращает вновь созданный элемент в отклике.</span><span class="sxs-lookup"><span data-stu-id="52309-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="52309-175">Обновление элементов</span><span class="sxs-lookup"><span data-stu-id="52309-175">Updating Items</span></span>

<span data-ttu-id="52309-176">Изменение записей осуществляется с помощью HTTP-запросов PUT.</span><span class="sxs-lookup"><span data-stu-id="52309-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="52309-177">За исключением этого изменения, метод `Edit` практически идентичен `Create`.</span><span class="sxs-lookup"><span data-stu-id="52309-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="52309-178">Обратите внимание, что если запись не найдена, то действие `Edit` возвратит отклик `NotFound` (404).</span><span class="sxs-lookup"><span data-stu-id="52309-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="52309-179">Чтобы выполнить проверку с помощью Postman, измените команду на PUT.</span><span class="sxs-lookup"><span data-stu-id="52309-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="52309-180">Укажите обновленные данные объекта в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="52309-180">Specify the updated object data in the Body of the request.</span></span>

![Консоль Postman с командой PUT и откликом](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="52309-182">Этот метод возвращает отклик `NoContent` (204) при успешном выполнении, чтобы обеспечить согласованность с ранее существовавшими API.</span><span class="sxs-lookup"><span data-stu-id="52309-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="52309-183">Удаление элементов</span><span class="sxs-lookup"><span data-stu-id="52309-183">Deleting Items</span></span>

<span data-ttu-id="52309-184">Удаление записей сопровождается отправкой запросов DELETE в службу и передачей идентификатора удаляемого элемента.</span><span class="sxs-lookup"><span data-stu-id="52309-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="52309-185">Как и в случае с обновлениями, запросы несуществующих элементов будут получать отклики `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="52309-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="52309-186">В противном случае успешный запрос получит отклик `NoContent` (204).</span><span class="sxs-lookup"><span data-stu-id="52309-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="52309-187">Обратите внимание, что при тестировании функции удаления в тексте запроса не требуется никаких элементов.</span><span class="sxs-lookup"><span data-stu-id="52309-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Консоль Postman с командой DELETE и откликом](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="52309-189">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="52309-189">Prevent over-posting</span></span>

<span data-ttu-id="52309-190">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="52309-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="52309-191">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="52309-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="52309-192">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="52309-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="52309-193">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="52309-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="52309-194">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="52309-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="52309-195">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="52309-195">A DTO may be used to:</span></span>

* <span data-ttu-id="52309-196">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="52309-196">Prevent over-posting.</span></span>
* <span data-ttu-id="52309-197">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="52309-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="52309-198">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="52309-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="52309-199">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="52309-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="52309-200">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="52309-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="52309-201">Чтобы продемонстрировать подход DTO, см. раздел [предотвращение избыточной отправки](xref:tutorials/first-web-api#prevent-over-posting) .</span><span class="sxs-lookup"><span data-stu-id="52309-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="52309-202">Общие соглашения веб-API</span><span class="sxs-lookup"><span data-stu-id="52309-202">Common Web API Conventions</span></span>

<span data-ttu-id="52309-203">При разработке внутренних служб для своего приложения вам потребуется согласованный набор соглашений или политик для обработки сквозных задач.</span><span class="sxs-lookup"><span data-stu-id="52309-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="52309-204">Например, в приведенной выше службе на запросы запрашивает определенные записи, которые не были найдены, и был получен отклик `NotFound`, а не `BadRequest`.</span><span class="sxs-lookup"><span data-stu-id="52309-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="52309-205">Аналогичным образом, команды, выполненные для этой службы, которые передавались в привязанные типы модели, всегда проверяли `ModelState.IsValid` и возвращали `BadRequest` для недопустимых типов модели.</span><span class="sxs-lookup"><span data-stu-id="52309-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="52309-206">Определив общую политику для своих API, вы обычно можете инкапсулировать ее в [фильтр](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="52309-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="52309-207">Дополнительные сведения о том, [как инкапсулировать общие политики API в приложения ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="52309-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52309-208">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="52309-208">Additional resources</span></span>

- [<span data-ttu-id="52309-209">Xamarin. Forms: проверка подлинности веб-службы</span><span class="sxs-lookup"><span data-stu-id="52309-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="52309-210">Xamarin. Forms: использование веб-службы RESTFUL</span><span class="sxs-lookup"><span data-stu-id="52309-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="52309-211">Microsoft Learn: использование веб-служб RESTFUL в приложениях Xamarin</span><span class="sxs-lookup"><span data-stu-id="52309-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="52309-212">Microsoft Learn. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52309-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
