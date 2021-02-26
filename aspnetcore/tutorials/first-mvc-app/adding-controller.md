---
title: Часть 2. Добавление контроллера в приложение MVC ASP.NET Core
author: rick-anderson
description: Часть 2 серии руководств по ASP.NET Core MVC.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975265"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="cde08-103">Часть 2. Добавление контроллера в приложение MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cde08-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="cde08-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="cde08-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cde08-105">Модель архитектуры MVC разделяет приложение на три основных компонента: **M** odel (модель), **V** iew (представление) и **C** ontroller (контроллер).</span><span class="sxs-lookup"><span data-stu-id="cde08-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="cde08-106">С помощью модели MVC можно создавать приложения, которые удобнее тестировать и обновлять по сравнению с традиционными монолитными приложениями.</span><span class="sxs-lookup"><span data-stu-id="cde08-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="cde08-107">Приложения на основе модели MVC содержат следующее:</span><span class="sxs-lookup"><span data-stu-id="cde08-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="cde08-108">**Модели**: классы, представляющие данные в приложении.</span><span class="sxs-lookup"><span data-stu-id="cde08-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="cde08-109">Классы модели используют логику проверки, которая позволяет применять бизнес-правила к этим данным.</span><span class="sxs-lookup"><span data-stu-id="cde08-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="cde08-110">Как правило, объекты модели извлекают и сохраняют состояние модели в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cde08-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="cde08-111">В этом руководстве модель `Movie` извлекает сведения о фильмах из базы данных и передает их в представление или обновляет.</span><span class="sxs-lookup"><span data-stu-id="cde08-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="cde08-112">Обновленные данные записываются в базу данных.</span><span class="sxs-lookup"><span data-stu-id="cde08-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="cde08-113">**Представления**: компоненты, которые формируют пользовательский интерфейс приложения.</span><span class="sxs-lookup"><span data-stu-id="cde08-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="cde08-114">Как правило, в пользовательском интерфейсе отображаются данные модели.</span><span class="sxs-lookup"><span data-stu-id="cde08-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="cde08-115">**Контроллеры**: классы, которые:</span><span class="sxs-lookup"><span data-stu-id="cde08-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="cde08-116">обрабатывают запросы браузера;</span><span class="sxs-lookup"><span data-stu-id="cde08-116">Handle browser requests.</span></span>
  * <span data-ttu-id="cde08-117">получают данные модели;</span><span class="sxs-lookup"><span data-stu-id="cde08-117">Retrieve model data.</span></span>
  * <span data-ttu-id="cde08-118">вызывают шаблоны представления вызовов, которые возвращают ответ.</span><span class="sxs-lookup"><span data-stu-id="cde08-118">Call view templates that return a response.</span></span>

<span data-ttu-id="cde08-119">В приложении MVC представление используется только для отображения информации.</span><span class="sxs-lookup"><span data-stu-id="cde08-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="cde08-120">Контроллер обрабатывает и реагирует на ввод и взаимодействие пользователя.</span><span class="sxs-lookup"><span data-stu-id="cde08-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="cde08-121">Например, контроллер обрабатывает сегменты URL-адреса и значения строки запроса и передает эти значения в модель.</span><span class="sxs-lookup"><span data-stu-id="cde08-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="cde08-122">Модель может использовать эти значения для выполнения запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="cde08-122">The model might use these values to query the database.</span></span> <span data-ttu-id="cde08-123">Пример.</span><span class="sxs-lookup"><span data-stu-id="cde08-123">For example:</span></span>

* <span data-ttu-id="cde08-124">`Https://localhost:5001/Home/Privacy`: задает контроллер `Home` и действие `Privacy`.</span><span class="sxs-lookup"><span data-stu-id="cde08-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="cde08-125">`Https://localhost:5001/Movies/Edit/5`: это запрос на изменение фильма с ID=5 с помощью контроллера `Movies` и действия `Edit`, которые подробно описаны далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cde08-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="cde08-126">Данные маршрута описаны далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cde08-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="cde08-127">Структура архитектуры MVC разделяет приложение на три основных группы компонентов: модели, представления и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="cde08-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="cde08-128">Этот шаблон помогает реализовать принципы разделения задач: логика пользовательского интерфейса относится к представлению.</span><span class="sxs-lookup"><span data-stu-id="cde08-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="cde08-129">Логика ввода относится к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="cde08-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="cde08-130">Бизнес-логика размещается в модели.</span><span class="sxs-lookup"><span data-stu-id="cde08-130">Business logic belongs in the model.</span></span> <span data-ttu-id="cde08-131">Подобное разделение позволяет эффективно справляться с трудностями при построении приложения, поскольку оно дает возможность работать одновременно с одним аспектом реализации, не затрагивая при этом код других.</span><span class="sxs-lookup"><span data-stu-id="cde08-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="cde08-132">Например, вы можете изменять код представления независимо от кода бизнес-логики.</span><span class="sxs-lookup"><span data-stu-id="cde08-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="cde08-133">Эти принципы продемонстрированы в этой серии руководств при создании приложения для работы с фильмами.</span><span class="sxs-lookup"><span data-stu-id="cde08-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="cde08-134">Проект MVC содержит папки для *контроллеров* и *представлений*.</span><span class="sxs-lookup"><span data-stu-id="cde08-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="cde08-135">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="cde08-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cde08-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cde08-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cde08-137">В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры > Добавить > Контроллер**.</span><span class="sxs-lookup"><span data-stu-id="cde08-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![Обозреватель решений: контекстное меню "Контроллеры > Добавить > Контроллер".](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="cde08-139">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC — пустой**.</span><span class="sxs-lookup"><span data-stu-id="cde08-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![Добавьте контроллер MVC и присвойте ему имя.](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="cde08-141">В диалоговом окне **Добавление нового элемента — MvcMovie** введите **HelloWorldController.cs** и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cde08-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cde08-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cde08-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cde08-143">Щелкните значок **обозревателя**, а затем правой кнопкой мыши щелкните **Контроллеры > Новый файл** и назовите новый файл *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="cde08-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![Контекстное меню](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cde08-145">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cde08-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cde08-146">В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры > Добавить > Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="cde08-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Контекстное меню](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="cde08-148">Выберите **ASP.NET Core** и **Класс контроллера**.</span><span class="sxs-lookup"><span data-stu-id="cde08-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="cde08-149">Присвойте контроллеру имя **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="cde08-149">Name the controller **HelloWorldController**.</span></span>

![Добавьте контроллер MVC и присвойте ему имя.](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="cde08-151">Замените содержимое файла *Controllers/HelloWorldController.cs* следующим:</span><span class="sxs-lookup"><span data-stu-id="cde08-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="cde08-152">Каждый метод `public` в контроллере вызывается как конечная точка HTTP.</span><span class="sxs-lookup"><span data-stu-id="cde08-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="cde08-153">В приведенном выше примере оба метода возвращают строку.</span><span class="sxs-lookup"><span data-stu-id="cde08-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="cde08-154">Обратите внимание на комментарии, предшествующие каждому методу.</span><span class="sxs-lookup"><span data-stu-id="cde08-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="cde08-155">Конечная точка HTTP:</span><span class="sxs-lookup"><span data-stu-id="cde08-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="cde08-156">Это URL-адрес, который является целевым в веб-приложении, например `https://localhost:5001/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="cde08-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="cde08-157">Она объединяет:</span><span class="sxs-lookup"><span data-stu-id="cde08-157">Combines:</span></span>
  * <span data-ttu-id="cde08-158">используемый протокол: `HTTPS`;</span><span class="sxs-lookup"><span data-stu-id="cde08-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="cde08-159">сетевое расположение веб-сервера, включая порт TCP: `localhost:5001`;</span><span class="sxs-lookup"><span data-stu-id="cde08-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="cde08-160">целевой универсальный код ресурса (URI): `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="cde08-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="cde08-161">В первом комментарии указано, что этот метод [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) вызывается путем добавления `/HelloWorld/` к базовому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="cde08-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="cde08-162">Во втором комментарии указано, что этот метод [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) вызывается путем добавления `/HelloWorld/Welcome/` к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="cde08-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="cde08-163">Далее в этом руководстве используется механизм формирования шаблонов для создания методов `HTTP POST`, которые обновляют данные.</span><span class="sxs-lookup"><span data-stu-id="cde08-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="cde08-164">Запустите приложение без отладчика.</span><span class="sxs-lookup"><span data-stu-id="cde08-164">Run the app without the debugger.</span></span>

<span data-ttu-id="cde08-165">Добавьте "HelloWorld" к пути в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="cde08-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="cde08-166">Метод `Index` возвращает строку.</span><span class="sxs-lookup"><span data-stu-id="cde08-166">The `Index` method returns a string.</span></span>

![Окно браузера, в котором отображается ответ "This is my default action" (Это действие по умолчанию)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="cde08-168">MVC вызывает классы контроллера (и методы действия в них) в зависимости от входящего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="cde08-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="cde08-169">[Логика маршрутизации URL-адресов](xref:mvc/controllers/routing), используемая моделью MVC по умолчанию, определяет вызываемый код на основе формата следующего вида:</span><span class="sxs-lookup"><span data-stu-id="cde08-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="cde08-170">Формат маршрутизации задается в методе `Configure` в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cde08-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="cde08-171">Если при переходе к приложению не указаны сегменты URL-адреса, по умолчанию устанавливаются контроллер Home и метод Index, которые заданы в выделенной выше строке шаблона.</span><span class="sxs-lookup"><span data-stu-id="cde08-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="cde08-172">В предшествующих сегментах URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="cde08-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="cde08-173">Первый сегмент URL-адреса определяет класс контроллера, который будет выполняться.</span><span class="sxs-lookup"><span data-stu-id="cde08-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="cde08-174">Поэтому `localhost:5001/HelloWorld` сопоставляется с классом **HelloWorld** Controller.</span><span class="sxs-lookup"><span data-stu-id="cde08-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="cde08-175">Вторая часть сегмента URL-адреса определяет метод действия для класса.</span><span class="sxs-lookup"><span data-stu-id="cde08-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="cde08-176">Таким образом, `localhost:5001/HelloWorld/Index` выполняет метод `Index` класса `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="cde08-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="cde08-177">Обратите внимание, что в этом случае достаточно перейти по адресу `localhost:5001/HelloWorld`, а метод `Index` вызывается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cde08-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="cde08-178">Если имя вызываемого метода не указано явно, для контроллера вызывается метод по умолчанию `Index`.</span><span class="sxs-lookup"><span data-stu-id="cde08-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="cde08-179">В третьей части сегмента URL-адреса (`id`) указываются данные маршрута.</span><span class="sxs-lookup"><span data-stu-id="cde08-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="cde08-180">Данные маршрута описаны далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cde08-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="cde08-181">Перейдите по адресу `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="cde08-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="cde08-182">Замените `{PORT}` на номер порта.</span><span class="sxs-lookup"><span data-stu-id="cde08-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="cde08-183">Метод `Welcome` запускается и возвращает строку `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="cde08-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="cde08-184">Для этого URL-адреса заданы контроллер `HelloWorld` и метод действия `Welcome`.</span><span class="sxs-lookup"><span data-stu-id="cde08-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="cde08-185">Часть URL-адреса `[Parameters]` на данный момент еще не использовалась.</span><span class="sxs-lookup"><span data-stu-id="cde08-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Окно браузера, в котором отображается ответ "This is the Welcome action method" (Это метод действия Welcome)](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="cde08-187">Измените код, чтобы передать сведения о параметрах из URL-адреса в контроллер.</span><span class="sxs-lookup"><span data-stu-id="cde08-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="cde08-188">Например, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="cde08-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="cde08-189">Измените метод `Welcome`, включив два параметра, как показано в следующем коде.</span><span class="sxs-lookup"><span data-stu-id="cde08-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="cde08-190">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="cde08-190">The preceding code:</span></span>

* <span data-ttu-id="cde08-191">Использует функцию необязательного параметра C#, указывая, что параметр `numTimes` по умолчанию принимает значение 1, если ему не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="cde08-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="cde08-192">Использует `HtmlEncoder.Default.Encode` для защиты приложения от злонамеренного ввода данных (например, JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cde08-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="cde08-193">Использует [интерполированные строки](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) в `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="cde08-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="cde08-194">Запустите приложение и перейдите по адресу `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="cde08-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="cde08-195">Замените `{PORT}` на номер порта.</span><span class="sxs-lookup"><span data-stu-id="cde08-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="cde08-196">Попробуйте использовать разные значения для `name` и `numtimes` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="cde08-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="cde08-197">Система [привязки модели](xref:mvc/models/model-binding) MVC автоматически сопоставляет именованные параметры из строки запроса с параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="cde08-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="cde08-198">Дополнительные сведения см. в разделе [Привязка модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="cde08-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Окно браузера, в котором отображается ответ Hello Rick; значение NumTimes равно 4.](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="cde08-200">На предыдущем рисунке:</span><span class="sxs-lookup"><span data-stu-id="cde08-200">In the previous image:</span></span>

* <span data-ttu-id="cde08-201">Сегмент URL-адреса `Parameters` не используется.</span><span class="sxs-lookup"><span data-stu-id="cde08-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="cde08-202">Параметры `name` и `numTimes` передаются в [строку запроса](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="cde08-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="cde08-203">Вопросительный знак (`?`) в приведенном выше URL-адресе используется в качестве разделителя, после которого указывается строка запроса.</span><span class="sxs-lookup"><span data-stu-id="cde08-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="cde08-204">Символом `&` отделяются пары "поле-значение".</span><span class="sxs-lookup"><span data-stu-id="cde08-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="cde08-205">Замените метод `Welcome` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="cde08-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="cde08-206">Запустите приложение и введите следующий URL-адрес: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="cde08-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="cde08-207">В предыдущем URL-адресе:</span><span class="sxs-lookup"><span data-stu-id="cde08-207">In the preceding URL:</span></span>

* <span data-ttu-id="cde08-208">Третий сегмент URL-адреса сопоставляется с параметром маршрута `id`.</span><span class="sxs-lookup"><span data-stu-id="cde08-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="cde08-209">Метод `Welcome` содержит параметр `id`, который сопоставляется с шаблоном URL-адреса в методе `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="cde08-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="cde08-210">Завершающий символ `?` (в `id?`) указывает, что параметр `id` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="cde08-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="cde08-211">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="cde08-211">In the preceding example:</span></span>

* <span data-ttu-id="cde08-212">Третий сегмент URL-адреса сопоставляется с параметром маршрута `id`.</span><span class="sxs-lookup"><span data-stu-id="cde08-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="cde08-213">Метод `Welcome` содержит параметр `id`, который сопоставляется с шаблоном URL-адреса в методе `MapControllerRoute`.</span><span class="sxs-lookup"><span data-stu-id="cde08-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="cde08-214">Завершающий символ `?` (в `id?`) указывает, что параметр `id` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="cde08-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cde08-215">[Назад: Приступая к работе](start-mvc.md)
> [Далее: Добавление представления](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="cde08-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cde08-216">Модель архитектуры MVC разделяет приложение на три основных компонента: **M** odel (модель), **V** iew (представление) и **C** ontroller (контроллер).</span><span class="sxs-lookup"><span data-stu-id="cde08-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="cde08-217">С помощью модели MVC можно создавать приложения, которые удобнее тестировать и обновлять по сравнению с традиционными монолитными приложениями.</span><span class="sxs-lookup"><span data-stu-id="cde08-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="cde08-218">Приложения на основе модели MVC содержат следующее:</span><span class="sxs-lookup"><span data-stu-id="cde08-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="cde08-219">**Модели**: классы, представляющие данные в приложении.</span><span class="sxs-lookup"><span data-stu-id="cde08-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="cde08-220">Классы модели используют логику проверки, которая позволяет применять бизнес-правила к этим данным.</span><span class="sxs-lookup"><span data-stu-id="cde08-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="cde08-221">Как правило, объекты модели извлекают и сохраняют состояние модели в базе данных.</span><span class="sxs-lookup"><span data-stu-id="cde08-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="cde08-222">В этом руководстве модель `Movie` извлекает сведения о фильмах из базы данных и передает их в представление или обновляет.</span><span class="sxs-lookup"><span data-stu-id="cde08-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="cde08-223">Обновленные данные записываются в базу данных.</span><span class="sxs-lookup"><span data-stu-id="cde08-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="cde08-224">**Представления**: компоненты, которые формируют пользовательский интерфейс приложения.</span><span class="sxs-lookup"><span data-stu-id="cde08-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="cde08-225">Как правило, в пользовательском интерфейсе отображаются данные модели.</span><span class="sxs-lookup"><span data-stu-id="cde08-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="cde08-226">**Контроллеры**: классы, которые обрабатывают запросы браузера.</span><span class="sxs-lookup"><span data-stu-id="cde08-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="cde08-227">Они извлекают данные модели и вызывают шаблоны представлений, которые возвращают ответ.</span><span class="sxs-lookup"><span data-stu-id="cde08-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="cde08-228">В приложении MVC представление служит только для отображения информации. Обработку введенных данных, формирование ответа и взаимодействие с пользователем обеспечивает контроллер.</span><span class="sxs-lookup"><span data-stu-id="cde08-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="cde08-229">Например, контроллер обрабатывает данные маршрута и значения строки запроса, после чего передает эти значения в модель.</span><span class="sxs-lookup"><span data-stu-id="cde08-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="cde08-230">Модель может использовать эти значения для выполнения запросов к базе данных.</span><span class="sxs-lookup"><span data-stu-id="cde08-230">The model might use these values to query the database.</span></span> <span data-ttu-id="cde08-231">Например, `https://localhost:5001/Home/About` содержит данные маршрута для `Home` (контроллер) и метода действия `About` (вызывает контроллер home).</span><span class="sxs-lookup"><span data-stu-id="cde08-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="cde08-232">`https://localhost:5001/Movies/Edit/5` — это запрос на редактирование фильма с идентификатором 5 с использованием контроллера movie.</span><span class="sxs-lookup"><span data-stu-id="cde08-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="cde08-233">Данные маршрута описаны далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cde08-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="cde08-234">Модель MVC позволяет создавать приложения, которые разделяют различные аспекты (логика ввода, бизнес-логика и логика пользовательского интерфейса), обеспечивая при этом слабые взаимозависимости между этими элементами.</span><span class="sxs-lookup"><span data-stu-id="cde08-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="cde08-235">Модель определяет, в каком месте приложения будет располагаться логика разных видов.</span><span class="sxs-lookup"><span data-stu-id="cde08-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="cde08-236">Логика пользовательского интерфейса относится к представлению.</span><span class="sxs-lookup"><span data-stu-id="cde08-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="cde08-237">Логика ввода относится к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="cde08-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="cde08-238">Бизнес-логика размещается в модели.</span><span class="sxs-lookup"><span data-stu-id="cde08-238">Business logic belongs in the model.</span></span> <span data-ttu-id="cde08-239">Подобное разделение позволяет эффективно справляться с трудностями при построении приложения, поскольку оно дает возможность работать одновременно с одним аспектом реализации, не затрагивая при этом код других.</span><span class="sxs-lookup"><span data-stu-id="cde08-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="cde08-240">Например, вы можете изменять код представления независимо от кода бизнес-логики.</span><span class="sxs-lookup"><span data-stu-id="cde08-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="cde08-241">Эти понятия подробно рассматриваются в данной серии руководств и наглядно демонстрируются при построении приложения movie.</span><span class="sxs-lookup"><span data-stu-id="cde08-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="cde08-242">Проект MVC содержит папки для *контроллеров* и *представлений*.</span><span class="sxs-lookup"><span data-stu-id="cde08-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="cde08-243">Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="cde08-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cde08-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cde08-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cde08-245">В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры > Добавить > Контроллер**</span><span class="sxs-lookup"><span data-stu-id="cde08-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![Контекстное меню](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="cde08-247">В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC — пустой**.</span><span class="sxs-lookup"><span data-stu-id="cde08-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![Добавьте контроллер MVC и присвойте ему имя.](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="cde08-249">В **диалоговом окне "Добавить пустой контроллер MVC"** введите **HelloWorldController** и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="cde08-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cde08-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cde08-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cde08-251">Щелкните значок **обозревателя**, а затем правой кнопкой мыши щелкните **Контроллеры > Новый файл** и назовите новый файл *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="cde08-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![Контекстное меню](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cde08-253">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="cde08-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cde08-254">В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры > Добавить > Новый файл**.</span><span class="sxs-lookup"><span data-stu-id="cde08-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Контекстное меню](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="cde08-256">Выберите **ASP.NET Core** и **Класс контроллера MVC**.</span><span class="sxs-lookup"><span data-stu-id="cde08-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="cde08-257">Присвойте контроллеру имя **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="cde08-257">Name the controller **HelloWorldController**.</span></span>

![Добавьте контроллер MVC и присвойте ему имя.](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="cde08-259">Замените содержимое файла *Controllers/HelloWorldController.cs* следующим:</span><span class="sxs-lookup"><span data-stu-id="cde08-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="cde08-260">Каждый метод `public` в контроллере вызывается как конечная точка HTTP.</span><span class="sxs-lookup"><span data-stu-id="cde08-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="cde08-261">В приведенном выше примере оба метода возвращают строку.</span><span class="sxs-lookup"><span data-stu-id="cde08-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="cde08-262">Обратите внимание на комментарии, предшествующие каждому методу.</span><span class="sxs-lookup"><span data-stu-id="cde08-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="cde08-263">Конечная точка HTTP — это адресуемый URL в веб-приложении, такой как `https://localhost:5001/HelloWorld`, который объединяет в себе используемый протокол (`HTTPS`), сетевое расположение сервера с TCP-портом (`localhost:5001`), а также целевой URI (`HelloWorld`).</span><span class="sxs-lookup"><span data-stu-id="cde08-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="cde08-264">В первом комментарии указано, что этот метод [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) вызывается путем добавления `/HelloWorld/` к базовому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="cde08-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="cde08-265">Во втором комментарии указано, что этот метод [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) вызывается путем добавления `/HelloWorld/Welcome/` к URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="cde08-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="cde08-266">Далее в этом руководстве используется механизм формирования шаблонов для создания методов `HTTP POST`, которые обновляют данные.</span><span class="sxs-lookup"><span data-stu-id="cde08-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="cde08-267">Запустите приложение в режиме без отладки и добавьте "HelloWorld" к пути в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="cde08-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="cde08-268">Метод `Index` возвращает строку.</span><span class="sxs-lookup"><span data-stu-id="cde08-268">The `Index` method returns a string.</span></span>

![Окно браузера, в котором отображается ответ "This is my default action" (Это действие по умолчанию)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="cde08-270">MVC вызывает классы контроллера (и методы действия в них) в зависимости от входящего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="cde08-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="cde08-271">[Логика маршрутизации URL-адресов](xref:mvc/controllers/routing), используемая моделью MVC по умолчанию, определяет вызываемый код на основе формата следующего вида:</span><span class="sxs-lookup"><span data-stu-id="cde08-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="cde08-272">Формат маршрутизации задается в методе `Configure` в файле *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cde08-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="cde08-273">Если при переходе к приложению не указаны сегменты URL-адреса, по умолчанию устанавливаются контроллер Home и метод Index, которые заданы в выделенной выше строке шаблона.</span><span class="sxs-lookup"><span data-stu-id="cde08-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="cde08-274">Первый сегмент URL-адреса определяет класс контроллера, который будет выполняться.</span><span class="sxs-lookup"><span data-stu-id="cde08-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="cde08-275">Таким образом, `localhost:{PORT}/HelloWorld` сопоставляется с классом `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="cde08-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="cde08-276">Вторая часть сегмента URL-адреса определяет метод действия для класса.</span><span class="sxs-lookup"><span data-stu-id="cde08-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="cde08-277">Таким образом, `localhost:{PORT}/HelloWorld/Index` выполняет метод `Index` класса `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="cde08-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="cde08-278">Обратите внимание, что в этом случае достаточно перейти по адресу `localhost:{PORT}/HelloWorld`, а метод `Index` вызывается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="cde08-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="cde08-279">Это связано с тем, что если имя вызываемого метода не задано явно, для контроллера по умолчанию вызывается метод `Index`.</span><span class="sxs-lookup"><span data-stu-id="cde08-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="cde08-280">В третьей части сегмента URL-адреса (`id`) указываются данные маршрута.</span><span class="sxs-lookup"><span data-stu-id="cde08-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="cde08-281">Данные маршрута описаны далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="cde08-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="cde08-282">Перейдите по адресу `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="cde08-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="cde08-283">Метод `Welcome` запускается и возвращает строку `This is the Welcome action method...`.</span><span class="sxs-lookup"><span data-stu-id="cde08-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="cde08-284">Для этого URL-адреса заданы контроллер `HelloWorld` и метод действия `Welcome`.</span><span class="sxs-lookup"><span data-stu-id="cde08-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="cde08-285">Часть URL-адреса `[Parameters]` на данный момент еще не использовалась.</span><span class="sxs-lookup"><span data-stu-id="cde08-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Окно браузера, в котором отображается ответ "This is the Welcome action method" (Это метод действия Welcome)](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="cde08-287">Измените код, чтобы передать сведения о параметрах из URL-адреса в контроллер.</span><span class="sxs-lookup"><span data-stu-id="cde08-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="cde08-288">Например, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="cde08-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="cde08-289">Измените метод `Welcome`, включив два параметра, как показано в следующем коде.</span><span class="sxs-lookup"><span data-stu-id="cde08-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="cde08-290">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="cde08-290">The preceding code:</span></span>

* <span data-ttu-id="cde08-291">Использует функцию необязательного параметра C#, указывая, что параметр `numTimes` по умолчанию принимает значение 1, если ему не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="cde08-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="cde08-292">Использует `HtmlEncoder.Default.Encode` для защиты приложения от злонамеренного ввода данных (JavaScript).</span><span class="sxs-lookup"><span data-stu-id="cde08-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="cde08-293">Использует [интерполированные строки](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) в `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="cde08-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="cde08-294">Запустите приложение и перейдите по адресу:</span><span class="sxs-lookup"><span data-stu-id="cde08-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="cde08-295">(Замените `{PORT}` на номер порта.) Вы можете попробовать различные значения `name` и `numtimes` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="cde08-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="cde08-296">Система [привязки модели](xref:mvc/models/model-binding) MVC автоматически сопоставляет именованные параметры из строк запроса в адресной строке с параметрами метода.</span><span class="sxs-lookup"><span data-stu-id="cde08-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="cde08-297">Дополнительные сведения см. в разделе [Привязка модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="cde08-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Окно браузера, в котором отображается ответ Hello Rick; значение NumTimes равно 4.](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="cde08-299">На показанном выше изображении сегмент URL-адреса (`Parameters`) не используется, а параметры `name` и `numTimes` передаются в [строке запроса](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="cde08-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="cde08-300">Вопросительный знак (`?`) в приведенном выше URL-адресе используется в качестве разделителя, после которого указывается строка запроса.</span><span class="sxs-lookup"><span data-stu-id="cde08-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="cde08-301">Символом `&` отделяются пары "поле-значение".</span><span class="sxs-lookup"><span data-stu-id="cde08-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="cde08-302">Замените метод `Welcome` следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="cde08-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="cde08-303">Запустите приложение и введите следующий URL-адрес: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="cde08-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="cde08-304">На этот раз третий сегмент URL-адреса сопоставляется с параметром маршрута `id`.</span><span class="sxs-lookup"><span data-stu-id="cde08-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="cde08-305">Метод `Welcome` содержит параметр `id`, который сопоставляется с шаблоном URL-адреса в методе `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="cde08-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="cde08-306">Завершающий символ `?` (в `id?`) указывает, что параметр `id` является необязательным.</span><span class="sxs-lookup"><span data-stu-id="cde08-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="cde08-307">В этих примерах контроллер реализует работу представления и контроллера в модели MVC.</span><span class="sxs-lookup"><span data-stu-id="cde08-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="cde08-308">Контроллер возвращает HTML напрямую.</span><span class="sxs-lookup"><span data-stu-id="cde08-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="cde08-309">Как правило, это не требуется, поскольку в этом случае заметно усложняется написание и поддержка кода.</span><span class="sxs-lookup"><span data-stu-id="cde08-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="cde08-310">Вместо этого обычно используется отдельный файл шаблона представления Razor для создания HTML-ответа.</span><span class="sxs-lookup"><span data-stu-id="cde08-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="cde08-311">Это будет показано в следующем руководстве.</span><span class="sxs-lookup"><span data-stu-id="cde08-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cde08-312">[Назад](start-mvc.md)
> [Вперед](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="cde08-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
