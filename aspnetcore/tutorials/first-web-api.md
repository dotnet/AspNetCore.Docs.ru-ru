---
title: Учебник. Создание веб-API с помощью ASP.NET Core
author: rick-anderson
description: Узнайте, как создать веб-API с помощью ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: 1f7c7db857090ff0a174d37b86e1265bab40b4fd
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564073"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="680c6-103">Учебник. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="680c6-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="680c6-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Майк Уоссон](https://github.com/mikewasson) (Mike Wasson)</span><span class="sxs-lookup"><span data-stu-id="680c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="680c6-105">В этом учебнике приводятся основные сведения о создании веб-API с помощью ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="680c6-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="680c6-106">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="680c6-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="680c6-107">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-107">Create a web API project.</span></span>
> * <span data-ttu-id="680c6-108">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="680c6-109">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="680c6-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="680c6-110">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="680c6-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="680c6-111">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-111">Call the web API with Postman.</span></span>

<span data-ttu-id="680c6-112">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="680c6-113">Обзор</span><span class="sxs-lookup"><span data-stu-id="680c6-113">Overview</span></span>

<span data-ttu-id="680c6-114">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="680c6-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="680c6-115">API</span><span class="sxs-lookup"><span data-stu-id="680c6-115">API</span></span> | <span data-ttu-id="680c6-116">Описание</span><span class="sxs-lookup"><span data-stu-id="680c6-116">Description</span></span> | <span data-ttu-id="680c6-117">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="680c6-117">Request body</span></span> | <span data-ttu-id="680c6-118">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="680c6-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="680c6-119">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-119">Get all to-do items</span></span> | <span data-ttu-id="680c6-120">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-120">None</span></span> | <span data-ttu-id="680c6-121">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="680c6-122">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="680c6-122">Get an item by ID</span></span> | <span data-ttu-id="680c6-123">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-123">None</span></span> | <span data-ttu-id="680c6-124">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="680c6-125">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="680c6-125">Add a new item</span></span> | <span data-ttu-id="680c6-126">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-126">To-do item</span></span> | <span data-ttu-id="680c6-127">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="680c6-128">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="680c6-129">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-129">To-do item</span></span> | <span data-ttu-id="680c6-130">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-130">None</span></span> |
|<span data-ttu-id="680c6-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="680c6-132">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="680c6-133">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-133">None</span></span> | <span data-ttu-id="680c6-134">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-134">None</span></span>|

<span data-ttu-id="680c6-135">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-135">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="680c6-141">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="680c6-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="680c6-145">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="680c6-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-147">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="680c6-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="680c6-148">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="680c6-149">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="680c6-150">Убедитесь, что в диалоговом окне **Создание веб-приложения ASP.NET Core** выбраны платформы **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="680c6-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="680c6-151">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-151">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="680c6-154">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="680c6-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="680c6-155">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="680c6-156">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="680c6-157">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="680c6-158">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-158">The preceding commands:</span></span>

  * <span data-ttu-id="680c6-159">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="680c6-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="680c6-160">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="680c6-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-161">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="680c6-162">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="680c6-162">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="680c6-164">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="680c6-165">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="680c6-167">В диалоговом окне **Configure the new ASP.NET Core Web API** (Настройка нового веб-API ASP.NET Core) в качестве **целевой платформы** выберите последнюю версию .NET Core 5.x.</span><span class="sxs-lookup"><span data-stu-id="680c6-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="680c6-168">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-168">Select **Next**.</span></span>

* <span data-ttu-id="680c6-169">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="680c6-171">Откройте окно терминала в папке проекта и выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="680c6-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="680c6-172">Тестирование проекта</span><span class="sxs-lookup"><span data-stu-id="680c6-172">Test the project</span></span>

<span data-ttu-id="680c6-173">Шаблон проекта создает API `WeatherForecast` с поддержкой [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="680c6-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="680c6-175">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="680c6-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="680c6-176">Visual Studio запустит:</span><span class="sxs-lookup"><span data-stu-id="680c6-176">Visual Studio launches:</span></span>

* <span data-ttu-id="680c6-177">веб-сервер IIS Express;</span><span class="sxs-lookup"><span data-stu-id="680c6-177">The IIS Express web server.</span></span>
* <span data-ttu-id="680c6-178">браузер по умолчанию и перейдет к `https://localhost:<port>/swagger/index.html`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="680c6-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="680c6-180">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="680c6-181">В браузере перейдите по следующему URL-адресу: [https://localhost:5001/swagger](https://localhost:5001/swagger).</span><span class="sxs-lookup"><span data-stu-id="680c6-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-182">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="680c6-183">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="680c6-184">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="680c6-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="680c6-185">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="680c6-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="680c6-186">Добавьте `/swagger` к URL-адресу (измените URL-адрес на `https://localhost:<port>/swagger`).</span><span class="sxs-lookup"><span data-stu-id="680c6-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="680c6-187">Откроется страница Swagger `/swagger/index.html`.</span><span class="sxs-lookup"><span data-stu-id="680c6-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="680c6-188">Выберите **Get** (Получить) > **Try it out** (Попробовать) > **Execute** (Выполнить).</span><span class="sxs-lookup"><span data-stu-id="680c6-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="680c6-189">На странице отобразятся:</span><span class="sxs-lookup"><span data-stu-id="680c6-189">The page displays:</span></span>

* <span data-ttu-id="680c6-190">команда [Curl](https://curl.haxx.se/) для тестирования API WeatherForecast;</span><span class="sxs-lookup"><span data-stu-id="680c6-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="680c6-191">URL-адрес для тестирования API WeatherForecast;</span><span class="sxs-lookup"><span data-stu-id="680c6-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="680c6-192">код, текст и заголовки ответа;</span><span class="sxs-lookup"><span data-stu-id="680c6-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="680c6-193">раскрывающийся список с типами мультимедиа и примером значения и схемы.</span><span class="sxs-lookup"><span data-stu-id="680c6-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="680c6-194">Swagger используется для создания полезной документации и страниц справки для веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="680c6-195">В этом учебнике рассматривается создание веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="680c6-196">Дополнительные сведения о Swagger: <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="680c6-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="680c6-197">Скопируйте и вставьте **URL-адрес запроса** в адресную строку браузера: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="680c6-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="680c6-198">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="680c6-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="680c6-199">Обновление launchUrl</span><span class="sxs-lookup"><span data-stu-id="680c6-199">Update the launchUrl</span></span>

<span data-ttu-id="680c6-200">В файле *Properties/launchSettings.json* обновите `launchUrl` с `"swagger"` на `"api/TodoItems"`.</span><span class="sxs-lookup"><span data-stu-id="680c6-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="680c6-201">Поскольку Swagger удален, предыдущая разметка изменит URL-адрес, который запускается в методе GET контроллера, добавленного в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="680c6-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="680c6-202">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="680c6-202">Add a model class</span></span>

<span data-ttu-id="680c6-203">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="680c6-204">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-206">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="680c6-207">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="680c6-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="680c6-208">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="680c6-209">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="680c6-210">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="680c6-211">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="680c6-213">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="680c6-214">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-215">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="680c6-216">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-216">Right-click the project.</span></span> <span data-ttu-id="680c6-217">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="680c6-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="680c6-218">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-218">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="680c6-220">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="680c6-221">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="680c6-222">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="680c6-223">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="680c6-224">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="680c6-225">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="680c6-225">Add a database context</span></span>

<span data-ttu-id="680c6-226">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="680c6-227">Этот класс является производным от класса <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="680c6-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="680c6-229">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="680c6-229">Add NuGet packages</span></span>

* <span data-ttu-id="680c6-230">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="680c6-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="680c6-231">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.InMemory** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="680c6-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="680c6-232">На панели слева выберите **Microsoft.EntityFrameworkCore.InMemory**.</span><span class="sxs-lookup"><span data-stu-id="680c6-232">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="680c6-233">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="680c6-235">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="680c6-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="680c6-236">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="680c6-237">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-238">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="680c6-239">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="680c6-240">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="680c6-241">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="680c6-241">Register the database context</span></span>

<span data-ttu-id="680c6-242">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="680c6-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="680c6-243">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="680c6-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="680c6-244">Обновите файл *Startup.cs*, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="680c6-245">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-245">The preceding code:</span></span>

* <span data-ttu-id="680c6-246">Удаляет вызовы Swagger.</span><span class="sxs-lookup"><span data-stu-id="680c6-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="680c6-247">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="680c6-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="680c6-248">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="680c6-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="680c6-249">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="680c6-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="680c6-250">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="680c6-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-252">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="680c6-253">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="680c6-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="680c6-254">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="680c6-255">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="680c6-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="680c6-256">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="680c6-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="680c6-257">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="680c6-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="680c6-258">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-259">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="680c6-260">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="680c6-261">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-261">The preceding commands:</span></span>

* <span data-ttu-id="680c6-262">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="680c6-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="680c6-263">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="680c6-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="680c6-264">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="680c6-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="680c6-265">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="680c6-265">The generated code:</span></span>

* <span data-ttu-id="680c6-266">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="680c6-267">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="680c6-268">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="680c6-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="680c6-269">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="680c6-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="680c6-270">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="680c6-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="680c6-271">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="680c6-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="680c6-272">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="680c6-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="680c6-273">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="680c6-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="680c6-274">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="680c6-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="680c6-275">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="680c6-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="680c6-276">Обновление метода создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="680c6-277">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="680c6-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="680c6-278">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="680c6-279">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="680c6-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="680c6-280">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="680c6-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="680c6-281">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="680c6-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="680c6-282">В случае успеха возвращает [код состояния HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201).</span><span class="sxs-lookup"><span data-stu-id="680c6-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="680c6-283">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="680c6-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="680c6-284">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="680c6-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="680c6-285">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="680c6-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="680c6-286">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="680c6-287">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="680c6-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="680c6-288">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="680c6-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="680c6-289">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-289">Install Postman</span></span>

<span data-ttu-id="680c6-290">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="680c6-291">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="680c6-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="680c6-292">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-292">Start the web app.</span></span>
* <span data-ttu-id="680c6-293">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-293">Start Postman.</span></span>
* <span data-ttu-id="680c6-294">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="680c6-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="680c6-295">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="680c6-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="680c6-296">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="680c6-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="680c6-297">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="680c6-298">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="680c6-298">Create a new request.</span></span>
* <span data-ttu-id="680c6-299">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="680c6-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="680c6-300">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="680c6-301">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="680c6-302">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="680c6-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="680c6-303">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="680c6-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="680c6-304">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="680c6-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="680c6-305">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="680c6-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="680c6-306">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-306">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="680c6-308">Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="680c6-308">Test the location header URI</span></span>

<span data-ttu-id="680c6-309">URI заголовка расположения можно протестировать в браузере.</span><span class="sxs-lookup"><span data-stu-id="680c6-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="680c6-310">Скопируйте и вставьте URI заголовка расположения в строку браузера.</span><span class="sxs-lookup"><span data-stu-id="680c6-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="680c6-311">Чтобы протестировать в Postman, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="680c6-311">To test in Postman:</span></span>

* <span data-ttu-id="680c6-312">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="680c6-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="680c6-313">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="680c6-313">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="680c6-315">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="680c6-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="680c6-316">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="680c6-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="680c6-317">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="680c6-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="680c6-318">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="680c6-319">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="680c6-319">Examine the GET methods</span></span>

<span data-ttu-id="680c6-320">Реализуются две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="680c6-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="680c6-321">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="680c6-322">Пример:</span><span class="sxs-lookup"><span data-stu-id="680c6-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="680c6-323">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="680c6-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="680c6-324">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-324">Test Get with Postman</span></span>

* <span data-ttu-id="680c6-325">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="680c6-325">Create a new request.</span></span>
* <span data-ttu-id="680c6-326">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="680c6-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="680c6-327">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="680c6-328">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="680c6-329">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="680c6-330">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-330">Select **Send**.</span></span>

<span data-ttu-id="680c6-331">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-331">This app uses an in-memory database.</span></span> <span data-ttu-id="680c6-332">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="680c6-333">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="680c6-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="680c6-334">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="680c6-334">Routing and URL paths</span></span>

<span data-ttu-id="680c6-335">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="680c6-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="680c6-336">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="680c6-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="680c6-337">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="680c6-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="680c6-338">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="680c6-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="680c6-339">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="680c6-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="680c6-340">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="680c6-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="680c6-341">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="680c6-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="680c6-342">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="680c6-342">This sample doesn't use a template.</span></span> <span data-ttu-id="680c6-343">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="680c6-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="680c6-344">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="680c6-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="680c6-345">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="680c6-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="680c6-346">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="680c6-346">Return values</span></span>

<span data-ttu-id="680c6-347">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="680c6-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="680c6-348">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="680c6-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="680c6-349">Код ответа для этого типа возвращаемого значения равен [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="680c6-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="680c6-350">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="680c6-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="680c6-351">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="680c6-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="680c6-352">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="680c6-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="680c6-353">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает код ошибки [ 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>.</span><span class="sxs-lookup"><span data-stu-id="680c6-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="680c6-354">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="680c6-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="680c6-355">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="680c6-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="680c6-356">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-356">The PutTodoItem method</span></span>

<span data-ttu-id="680c6-357">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="680c6-358">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="680c6-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="680c6-359">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="680c6-360">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="680c6-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="680c6-361">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="680c6-362">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="680c6-363">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="680c6-364">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="680c6-365">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="680c6-366">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="680c6-367">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя `"feed fish"`:</span><span class="sxs-lookup"><span data-stu-id="680c6-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="680c6-368">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="680c6-368">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="680c6-370">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="680c6-371">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="680c6-372">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="680c6-373">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="680c6-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="680c6-374">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="680c6-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="680c6-375">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="680c6-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="680c6-376">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="680c6-377">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="680c6-377">Prevent over-posting</span></span>

<span data-ttu-id="680c6-378">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="680c6-379">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="680c6-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="680c6-380">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="680c6-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="680c6-381">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="680c6-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="680c6-382">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="680c6-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="680c6-383">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="680c6-383">A DTO may be used to:</span></span>

* <span data-ttu-id="680c6-384">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="680c6-384">Prevent over-posting.</span></span>
* <span data-ttu-id="680c6-385">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="680c6-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="680c6-386">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="680c6-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="680c6-387">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="680c6-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="680c6-388">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="680c6-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="680c6-389">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="680c6-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="680c6-390">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="680c6-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="680c6-391">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="680c6-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="680c6-392">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="680c6-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="680c6-393">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="680c6-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="680c6-394">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="680c6-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="680c6-395">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="680c6-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="680c6-396">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="680c6-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="680c6-397">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="680c6-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="680c6-398">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-398">Create a web API project.</span></span>
> * <span data-ttu-id="680c6-399">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="680c6-400">Формирование шаблонов контроллера с использованием методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="680c6-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="680c6-401">Настройка маршрутизации, URL-пути и возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="680c6-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="680c6-402">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-402">Call the web API with Postman.</span></span>

<span data-ttu-id="680c6-403">В итоге вы получите веб-API, позволяющий работать с элементами списка дел, хранимыми в базе данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="680c6-404">Обзор</span><span class="sxs-lookup"><span data-stu-id="680c6-404">Overview</span></span>

<span data-ttu-id="680c6-405">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="680c6-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="680c6-406">API</span><span class="sxs-lookup"><span data-stu-id="680c6-406">API</span></span> | <span data-ttu-id="680c6-407">Описание</span><span class="sxs-lookup"><span data-stu-id="680c6-407">Description</span></span> | <span data-ttu-id="680c6-408">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="680c6-408">Request body</span></span> | <span data-ttu-id="680c6-409">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="680c6-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="680c6-410">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-410">Get all to-do items</span></span> | <span data-ttu-id="680c6-411">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-411">None</span></span> | <span data-ttu-id="680c6-412">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="680c6-413">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="680c6-413">Get an item by ID</span></span> | <span data-ttu-id="680c6-414">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-414">None</span></span> | <span data-ttu-id="680c6-415">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="680c6-416">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="680c6-416">Add a new item</span></span> | <span data-ttu-id="680c6-417">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-417">To-do item</span></span> | <span data-ttu-id="680c6-418">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="680c6-419">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="680c6-420">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-420">To-do item</span></span> | <span data-ttu-id="680c6-421">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-421">None</span></span> |
|<span data-ttu-id="680c6-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="680c6-423">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="680c6-424">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-424">None</span></span> | <span data-ttu-id="680c6-425">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-425">None</span></span>|

<span data-ttu-id="680c6-426">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-426">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="680c6-432">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="680c6-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-435">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="680c6-436">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="680c6-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-438">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="680c6-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="680c6-439">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="680c6-440">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="680c6-441">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="680c6-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="680c6-442">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-442">Select the **API** template and click **Create**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="680c6-445">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="680c6-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="680c6-446">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="680c6-447">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="680c6-448">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="680c6-449">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-449">The preceding commands:</span></span>

  * <span data-ttu-id="680c6-450">Создает новый проект веб-API и открывает его в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="680c6-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="680c6-451">Добавляет пакеты NuGet, которые понадобятся в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="680c6-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-452">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="680c6-453">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="680c6-453">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="680c6-455">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="680c6-456">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Выбор шаблона API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="680c6-458">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="680c6-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="680c6-459">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-459">Select **Next**.</span></span>

* <span data-ttu-id="680c6-460">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="680c6-462">Откройте окно терминала в папке проекта и выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="680c6-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="680c6-463">Тестирование API</span><span class="sxs-lookup"><span data-stu-id="680c6-463">Test the API</span></span>

<span data-ttu-id="680c6-464">Шаблон проекта создает API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="680c6-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="680c6-465">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="680c6-467">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="680c6-468">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/WeatherForecast`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="680c6-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="680c6-469">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="680c6-470">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="680c6-472">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="680c6-473">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="680c6-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-474">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="680c6-475">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="680c6-476">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="680c6-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="680c6-477">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="680c6-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="680c6-478">Добавьте `/WeatherForecast` к URL-адресу (измените URL-адрес на `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="680c6-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="680c6-479">Возвращаемые данные JSON будут выглядеть примерно так:</span><span class="sxs-lookup"><span data-stu-id="680c6-479">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="680c6-480">Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="680c6-480">Add a model class</span></span>

<span data-ttu-id="680c6-481">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="680c6-482">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-484">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="680c6-485">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="680c6-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="680c6-486">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="680c6-487">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="680c6-488">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="680c6-489">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="680c6-491">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="680c6-492">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-493">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="680c6-494">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-494">Right-click the project.</span></span> <span data-ttu-id="680c6-495">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="680c6-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="680c6-496">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-496">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="680c6-498">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="680c6-499">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="680c6-500">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="680c6-501">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="680c6-502">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="680c6-503">Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="680c6-503">Add a database context</span></span>

<span data-ttu-id="680c6-504">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="680c6-505">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="680c6-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="680c6-507">Добавление пакетов NuGet</span><span class="sxs-lookup"><span data-stu-id="680c6-507">Add NuGet packages</span></span>

* <span data-ttu-id="680c6-508">В меню **Сервис** выберите **Диспетчер пакетов NuGet > Управление пакетами NuGet для решения**.</span><span class="sxs-lookup"><span data-stu-id="680c6-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="680c6-509">Перейдите на вкладку **Обзор** и введите **Microsoft.EntityFrameworkCore.InMemory** в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="680c6-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="680c6-510">На панели слева выберите **Microsoft.EntityFrameworkCore.InMemory**.</span><span class="sxs-lookup"><span data-stu-id="680c6-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="680c6-511">Установите флажок **Проект** на правой панели и выберите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Диспетчер пакетов NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="680c6-513">Добавление контекста базы данных TodoContext</span><span class="sxs-lookup"><span data-stu-id="680c6-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="680c6-514">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="680c6-515">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-516">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="680c6-517">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="680c6-518">Введите следующий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="680c6-519">Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="680c6-519">Register the database context</span></span>

<span data-ttu-id="680c6-520">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="680c6-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="680c6-521">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="680c6-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="680c6-522">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="680c6-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="680c6-523">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-523">The preceding code:</span></span>

* <span data-ttu-id="680c6-524">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="680c6-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="680c6-525">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="680c6-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="680c6-526">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="680c6-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="680c6-527">Формирование шаблонов контроллера</span><span class="sxs-lookup"><span data-stu-id="680c6-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-529">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="680c6-530">Щелкните **Добавить** > **Создать шаблонный элемент**.</span><span class="sxs-lookup"><span data-stu-id="680c6-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="680c6-531">Выберите **Контроллер API с действиями, использующий Entity Framework**, а затем выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="680c6-532">В диалоговом окне **Контроллер API с действиями, использующий Entity Framework** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="680c6-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="680c6-533">Выберите **TodoItem (TodoApi.Models)** в поле **Класс модели**.</span><span class="sxs-lookup"><span data-stu-id="680c6-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="680c6-534">Выберите **TodoContext (TodoApi.Models)** в поле **Класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="680c6-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="680c6-535">Нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-536">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="680c6-537">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="680c6-538">Предыдущие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-538">The preceding commands:</span></span>

* <span data-ttu-id="680c6-539">добавляют пакеты NuGet, необходимые для формирования шаблонов;</span><span class="sxs-lookup"><span data-stu-id="680c6-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="680c6-540">устанавливают подсистему формирования шаблонов (`dotnet-aspnet-codegenerator`);</span><span class="sxs-lookup"><span data-stu-id="680c6-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="680c6-541">формируют шаблоны для `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="680c6-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="680c6-542">Сформированный код:</span><span class="sxs-lookup"><span data-stu-id="680c6-542">The generated code:</span></span>

* <span data-ttu-id="680c6-543">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="680c6-544">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="680c6-545">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="680c6-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="680c6-546">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="680c6-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="680c6-547">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="680c6-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="680c6-548">Шаблоны ASP.NET Core для:</span><span class="sxs-lookup"><span data-stu-id="680c6-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="680c6-549">Контроллеры с представлениями включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="680c6-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="680c6-550">Контроллеры API не включают `[action]` в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="680c6-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="680c6-551">Если токен `[action]` не находится в шаблоне маршрута, имя [действия](xref:mvc/controllers/routing#action) исключается из маршрута.</span><span class="sxs-lookup"><span data-stu-id="680c6-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="680c6-552">То есть имя связанного метода действия не используется в соответствующем маршруте.</span><span class="sxs-lookup"><span data-stu-id="680c6-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="680c6-553">Знакомство с методом создания PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="680c6-554">Измените инструкцию возврата в `PostTodoItem` и используйте оператор [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="680c6-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="680c6-555">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="680c6-556">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="680c6-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="680c6-557">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="680c6-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="680c6-558">Метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="680c6-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="680c6-559">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="680c6-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="680c6-560">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="680c6-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="680c6-561">Добавляет в ответ заголовок [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location).</span><span class="sxs-lookup"><span data-stu-id="680c6-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="680c6-562">Заголовок `Location` указывает [URI](https://developer.mozilla.org/docs/Glossary/URI) новой созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="680c6-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="680c6-563">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="680c6-564">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="680c6-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="680c6-565">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="680c6-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="680c6-566">Установка Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-566">Install Postman</span></span>

<span data-ttu-id="680c6-567">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="680c6-568">Установка [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="680c6-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="680c6-569">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-569">Start the web app.</span></span>
* <span data-ttu-id="680c6-570">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-570">Start Postman.</span></span>
* <span data-ttu-id="680c6-571">Отключение параметра **Проверка SSL-сертификата**</span><span class="sxs-lookup"><span data-stu-id="680c6-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="680c6-572">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="680c6-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="680c6-573">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="680c6-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="680c6-574">Тестирование PostTodoItem с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="680c6-575">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="680c6-575">Create a new request.</span></span>
* <span data-ttu-id="680c6-576">Установите HTTP-метод `POST`.</span><span class="sxs-lookup"><span data-stu-id="680c6-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="680c6-577">Задайте для URI значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="680c6-578">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="680c6-579">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="680c6-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="680c6-580">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="680c6-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="680c6-581">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="680c6-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="680c6-582">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="680c6-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="680c6-583">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-583">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="680c6-585">Тестирование URI заголовка расположения с помощью Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="680c6-586">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="680c6-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="680c6-587">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="680c6-587">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="680c6-589">Установите HTTP-метод `GET`.</span><span class="sxs-lookup"><span data-stu-id="680c6-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="680c6-590">Задайте для URI значение `https://localhost:<port>/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="680c6-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="680c6-591">Например, `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="680c6-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="680c6-592">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="680c6-593">Знакомство с методами GET</span><span class="sxs-lookup"><span data-stu-id="680c6-593">Examine the GET methods</span></span>

<span data-ttu-id="680c6-594">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="680c6-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="680c6-595">Протестируйте приложение, вызвав эти две конечные точки в браузере или в Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="680c6-596">Пример:</span><span class="sxs-lookup"><span data-stu-id="680c6-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="680c6-597">При вызове `GetTodoItems` возвращается примерно такой ответ:</span><span class="sxs-lookup"><span data-stu-id="680c6-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="680c6-598">Тестирование Get с использованием Postman</span><span class="sxs-lookup"><span data-stu-id="680c6-598">Test Get with Postman</span></span>

* <span data-ttu-id="680c6-599">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="680c6-599">Create a new request.</span></span>
* <span data-ttu-id="680c6-600">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="680c6-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="680c6-601">Задайте для URI запроса значение `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="680c6-602">Например, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="680c6-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="680c6-603">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="680c6-604">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-604">Select **Send**.</span></span>

<span data-ttu-id="680c6-605">Это приложение использует выполняющуюся в памяти базу данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-605">This app uses an in-memory database.</span></span> <span data-ttu-id="680c6-606">Если остановить и вновь запустить его, предшествующий запрос GET не возвратит никаких данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="680c6-607">Если данные не возвращаются, данные для приложения получаются методом [POST](#post).</span><span class="sxs-lookup"><span data-stu-id="680c6-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="680c6-608">Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="680c6-608">Routing and URL paths</span></span>

<span data-ttu-id="680c6-609">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="680c6-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="680c6-610">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="680c6-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="680c6-611">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="680c6-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="680c6-612">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="680c6-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="680c6-613">В этом примере класс контроллера имеет имя **TodoItems**, а сам контроллер, соответственно, — "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="680c6-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="680c6-614">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="680c6-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="680c6-615">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="680c6-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="680c6-616">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="680c6-616">This sample doesn't use a template.</span></span> <span data-ttu-id="680c6-617">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="680c6-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="680c6-618">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="680c6-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="680c6-619">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="680c6-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="680c6-620">Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="680c6-620">Return values</span></span> 

<span data-ttu-id="680c6-621">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="680c6-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="680c6-622">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="680c6-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="680c6-623">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="680c6-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="680c6-624">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="680c6-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="680c6-625">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="680c6-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="680c6-626">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="680c6-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="680c6-627">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="680c6-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="680c6-628">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="680c6-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="680c6-629">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="680c6-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="680c6-630">Метод PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-630">The PutTodoItem method</span></span>

<span data-ttu-id="680c6-631">Проверьте метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="680c6-632">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="680c6-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="680c6-633">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="680c6-634">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="680c6-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="680c6-635">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="680c6-636">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="680c6-637">Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="680c6-638">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="680c6-639">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="680c6-640">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="680c6-641">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="680c6-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="680c6-642">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="680c6-642">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="680c6-644">Метод DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="680c6-645">Проверьте метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="680c6-646">Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="680c6-647">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="680c6-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="680c6-648">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="680c6-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="680c6-649">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="680c6-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="680c6-650">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="680c6-651">Предотвращение избыточной публикации</span><span class="sxs-lookup"><span data-stu-id="680c6-651">Prevent over-posting</span></span>

<span data-ttu-id="680c6-652">В настоящее время пример приложения предоставляет весь объект `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="680c6-653">Рабочие приложения обычно ограничивают вводимые данные и возвращают их с помощью подмножества модели.</span><span class="sxs-lookup"><span data-stu-id="680c6-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="680c6-654">Это связано с несколькими причинами, и безопасность является основной.</span><span class="sxs-lookup"><span data-stu-id="680c6-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="680c6-655">Подмножество модели обычно называется объектом передачи данных (DTO), моделью ввода или моделью представления.</span><span class="sxs-lookup"><span data-stu-id="680c6-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="680c6-656">В этой статье используется **DTO**.</span><span class="sxs-lookup"><span data-stu-id="680c6-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="680c6-657">DTO можно использовать для следующего:</span><span class="sxs-lookup"><span data-stu-id="680c6-657">A DTO may be used to:</span></span>

* <span data-ttu-id="680c6-658">Предотвращение избыточной публикации.</span><span class="sxs-lookup"><span data-stu-id="680c6-658">Prevent over-posting.</span></span>
* <span data-ttu-id="680c6-659">Скрытие свойств, которые не предназначены для просмотра клиентами.</span><span class="sxs-lookup"><span data-stu-id="680c6-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="680c6-660">Пропуск некоторых свойств, чтобы уменьшить размер полезной нагрузки.</span><span class="sxs-lookup"><span data-stu-id="680c6-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="680c6-661">Сведение графов объектов, содержащих вложенные объекты.</span><span class="sxs-lookup"><span data-stu-id="680c6-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="680c6-662">Сведенные графы объектов могут быть удобнее для клиентов.</span><span class="sxs-lookup"><span data-stu-id="680c6-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="680c6-663">Чтобы продемонстрировать подход с применением DTO, обновите класс `TodoItem`, включив в него поле секрета:</span><span class="sxs-lookup"><span data-stu-id="680c6-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="680c6-664">Поле секрета должно быть скрыто в этом приложении, однако административное приложение может отобразить его.</span><span class="sxs-lookup"><span data-stu-id="680c6-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="680c6-665">Убедитесь, что вы можете отправить и получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="680c6-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="680c6-666">Создайте модель DTO:</span><span class="sxs-lookup"><span data-stu-id="680c6-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="680c6-667">Обновите `TodoItemsController` для использования `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="680c6-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="680c6-668">Убедитесь, что вы можете отправить или получить секретное поле.</span><span class="sxs-lookup"><span data-stu-id="680c6-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="680c6-669">Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="680c6-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="680c6-670">См. руководство по [: Вызовите веб-API ASP.NET Core с помощью JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="680c6-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="680c6-671">В этом руководстве вы узнаете, как:</span><span class="sxs-lookup"><span data-stu-id="680c6-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="680c6-672">Создание проекта веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-672">Create a web API project.</span></span>
> * <span data-ttu-id="680c6-673">Добавление класса модели и контекста базы данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="680c6-674">Добавление контроллера.</span><span class="sxs-lookup"><span data-stu-id="680c6-674">Add a controller.</span></span>
> * <span data-ttu-id="680c6-675">Добавление методов CRUD.</span><span class="sxs-lookup"><span data-stu-id="680c6-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="680c6-676">Настройка маршрутизации и путей URL.</span><span class="sxs-lookup"><span data-stu-id="680c6-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="680c6-677">Указание возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="680c6-677">Specify return values.</span></span>
> * <span data-ttu-id="680c6-678">Вызов веб-API с помощью Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="680c6-679">Вызовите веб-API с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="680c6-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="680c6-680">В конечном итоге вы получите веб-API, обеспечивающий управление элементами списка дел, хранящимися в реляционной базе данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="680c6-681">2\.1. Обзор</span><span class="sxs-lookup"><span data-stu-id="680c6-681">Overview 2.1</span></span>

<span data-ttu-id="680c6-682">В этом руководстве создается следующий API-интерфейс:</span><span class="sxs-lookup"><span data-stu-id="680c6-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="680c6-683">API</span><span class="sxs-lookup"><span data-stu-id="680c6-683">API</span></span> | <span data-ttu-id="680c6-684">Описание</span><span class="sxs-lookup"><span data-stu-id="680c6-684">Description</span></span> | <span data-ttu-id="680c6-685">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="680c6-685">Request body</span></span> | <span data-ttu-id="680c6-686">Текст ответа</span><span class="sxs-lookup"><span data-stu-id="680c6-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="680c6-687">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="680c6-687">GET /api/TodoItems</span></span> | <span data-ttu-id="680c6-688">Получение всех элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-688">Get all to-do items</span></span> | <span data-ttu-id="680c6-689">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-689">None</span></span> | <span data-ttu-id="680c6-690">Массив элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-690">Array of to-do items</span></span>|
|<span data-ttu-id="680c6-691">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="680c6-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="680c6-692">Получение объекта по идентификатору</span><span class="sxs-lookup"><span data-stu-id="680c6-692">Get an item by ID</span></span> | <span data-ttu-id="680c6-693">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-693">None</span></span> | <span data-ttu-id="680c6-694">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-694">To-do item</span></span>|
|<span data-ttu-id="680c6-695">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="680c6-695">POST /api/TodoItems</span></span> | <span data-ttu-id="680c6-696">Добавление нового элемента</span><span class="sxs-lookup"><span data-stu-id="680c6-696">Add a new item</span></span> | <span data-ttu-id="680c6-697">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-697">To-do item</span></span> | <span data-ttu-id="680c6-698">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-698">To-do item</span></span> |
|<span data-ttu-id="680c6-699">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="680c6-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="680c6-700">Обновление существующего элемента &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="680c6-701">Элемент задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-701">To-do item</span></span> | <span data-ttu-id="680c6-702">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-702">None</span></span> |
|<span data-ttu-id="680c6-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="680c6-704">Удаление элемента &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="680c6-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="680c6-705">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-705">None</span></span> | <span data-ttu-id="680c6-706">Отсутствуют</span><span class="sxs-lookup"><span data-stu-id="680c6-706">None</span></span>|

<span data-ttu-id="680c6-707">На следующем рисунке показана структура приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-707">The following diagram shows the design of the app.</span></span>

![Клиент представлен прямоугольником слева.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="680c6-713">2\.1. Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="680c6-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-714">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-716">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="680c6-717">2\.1. Создание веб-проекта</span><span class="sxs-lookup"><span data-stu-id="680c6-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-719">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="680c6-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="680c6-720">Выберите шаблон **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="680c6-721">Назовите проект *TodoApi* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="680c6-722">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="680c6-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="680c6-723">Выберите шаблон **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="680c6-724">**Не** выбирайте **Включение поддержки Docker**.</span><span class="sxs-lookup"><span data-stu-id="680c6-724">**Don't** select **Enable Docker Support**.</span></span>

![Диалоговое окно создания проекта VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="680c6-727">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="680c6-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="680c6-728">Смените каталог (`cd`) на папку, в которой будет содержаться папка проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="680c6-729">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="680c6-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="680c6-730">С помощью этих команд создается новый проект веб-API и открывается новый экземпляр Visual Studio Code в новой папке проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="680c6-731">При появлении диалогового окна с запросом на добавление в проект необходимых ресурсов выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-732">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="680c6-733">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="680c6-733">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="680c6-735">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="680c6-736">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **API** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="680c6-737">В диалоговом окне **Настройка нового веб-API ASP.NET Core** в качестве **целевой платформы** выберите последнюю версию .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="680c6-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="680c6-738">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="680c6-738">Select **Next**.</span></span>

* <span data-ttu-id="680c6-739">Введите *TodoApi* в поле **Имя проекта** и выберите команду **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![Диалоговое окно конфигурации](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="680c6-741">2\.1. Тестирование API</span><span class="sxs-lookup"><span data-stu-id="680c6-741">Test the API 2.1</span></span>

<span data-ttu-id="680c6-742">Шаблон проекта создает API `values`.</span><span class="sxs-lookup"><span data-stu-id="680c6-742">The project template creates a `values` API.</span></span> <span data-ttu-id="680c6-743">Вызовите метод `Get` из браузера для тестирования приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="680c6-745">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="680c6-746">Visual Studio запустит браузер и перейдет к `https://localhost:<port>/api/values`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="680c6-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="680c6-747">Если появится диалоговое окно с запросом о необходимости доверять сертификату IIS Express, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="680c6-748">В появляющемся следом диалоговом окне **Предупреждение системы безопасности** выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="680c6-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="680c6-750">Нажмите клавиши CTRL+F5, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="680c6-751">В браузере перейдите по следующему URL-адресу: `https://localhost:5001/api/values`.</span><span class="sxs-lookup"><span data-stu-id="680c6-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-752">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="680c6-753">Выберите **Выполнить** > **Начать отладку**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="680c6-754">Visual Studio для Mac запустит браузер и перейдет к `https://localhost:<port>`, где `<port>` — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="680c6-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="680c6-755">Появится ошибка HTTP 404 (Не найдено).</span><span class="sxs-lookup"><span data-stu-id="680c6-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="680c6-756">Добавьте `/api/values` к URL-адресу (измените URL-адрес на `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="680c6-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="680c6-757">Возвращается следующий файл JSON:</span><span class="sxs-lookup"><span data-stu-id="680c6-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="680c6-758">2\.1. Добавление класса модели</span><span class="sxs-lookup"><span data-stu-id="680c6-758">Add a model class 2.1</span></span>

<span data-ttu-id="680c6-759">*Модель* — это набор классов, представляющих данные, которыми управляет приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="680c6-760">Модель этого приложения содержит единственный класс `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-762">В **обозревателе решений** щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="680c6-763">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="680c6-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="680c6-764">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="680c6-765">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="680c6-766">Присвойте классу имя *TodoItem* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="680c6-767">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="680c6-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="680c6-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="680c6-769">Добавьте папку с именем *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="680c6-770">Добавьте в папку *Models* класс `TodoItem` со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="680c6-771">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="680c6-772">Щелкните проект правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-772">Right-click the project.</span></span> <span data-ttu-id="680c6-773">Выберите **Добавить** > **Новая папка**.</span><span class="sxs-lookup"><span data-stu-id="680c6-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="680c6-774">Назовите папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-774">Name the folder *Models*.</span></span>

  ![Новая папка](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="680c6-776">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Создать файл** > **Общие** > **Пустой класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="680c6-777">Назовите класс *TodoItem* и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="680c6-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="680c6-778">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="680c6-779">Свойство `Id` выступает в качестве уникального ключа реляционной базы данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="680c6-780">Классы моделей можно размещать в любом месте проекта, но обычно для этого используется папка *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="680c6-781">2\.1. Добавление контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="680c6-781">Add a database context 2.1</span></span>

<span data-ttu-id="680c6-782">*Контекст базы данных* —это основной класс, который координирует функциональные возможности Entity Framework для модели данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="680c6-783">Этот класс является производным от класса `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="680c6-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-785">Щелкните папку *Models* правой кнопкой мыши и выберите пункты **Добавить** > **Класс**.</span><span class="sxs-lookup"><span data-stu-id="680c6-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="680c6-786">Назовите класс *TodoContext* и нажмите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-787">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="680c6-788">Добавьте класс `TodoContext` в папку *Models* .</span><span class="sxs-lookup"><span data-stu-id="680c6-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="680c6-789">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="680c6-790">2\.1. Регистрация контекста базы данных</span><span class="sxs-lookup"><span data-stu-id="680c6-790">Register the database context 2.1</span></span>

<span data-ttu-id="680c6-791">В ASP.NET Core службы (такие как контекст базы данных) должны быть зарегистрированы с помощью контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="680c6-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="680c6-792">Контейнер предоставляет службу контроллерам.</span><span class="sxs-lookup"><span data-stu-id="680c6-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="680c6-793">Обновите файл *Startup.cs*, используя следующий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="680c6-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="680c6-794">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-794">The preceding code:</span></span>

* <span data-ttu-id="680c6-795">Удалите неиспользуемые объявления `using`.</span><span class="sxs-lookup"><span data-stu-id="680c6-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="680c6-796">Добавляет контекст базы данных в контейнер внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="680c6-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="680c6-797">Указывает, что контекст базы данных будет использовать базу данных в памяти.</span><span class="sxs-lookup"><span data-stu-id="680c6-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="680c6-798">2\.1. Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="680c6-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-800">Щелкните папку *Controllers* правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="680c6-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="680c6-801">Щелкните **Добавить** > **Создать элемент**.</span><span class="sxs-lookup"><span data-stu-id="680c6-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="680c6-802">В диалоговом окне **Добавить новый элемент** выберите шаблон **Класс контроллера API**.</span><span class="sxs-lookup"><span data-stu-id="680c6-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="680c6-803">Присвойте классу имя *TodoController* и выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![Диалоговое окно добавления элемента с контроллером в поле поиска и выбранным контроллером веб-API](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-805">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="680c6-806">В папке *Controllers* создайте класс с именем `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="680c6-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="680c6-807">Замените код шаблона следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="680c6-808">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="680c6-808">The preceding code:</span></span>

* <span data-ttu-id="680c6-809">Определяет класс контроллера API без методов.</span><span class="sxs-lookup"><span data-stu-id="680c6-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="680c6-810">Пометьте этот класс атрибутом [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="680c6-811">Этот атрибут указывает, что контроллер отвечает на запросы веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="680c6-812">Дополнительные сведения о поведении, которое реализует этот атрибут, см. в <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="680c6-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="680c6-813">Использует внедрение зависимостей для внедрения контекста базы данных (`TodoContext`) в контроллер.</span><span class="sxs-lookup"><span data-stu-id="680c6-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="680c6-814">Контекст базы данных используется в каждом методе [создания, чтения, обновления и удаления](https://wikipedia.org/wiki/Create,_read,_update_and_delete) в контроллере.</span><span class="sxs-lookup"><span data-stu-id="680c6-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="680c6-815">Добавляет элемент `Item1` в базу данных, если она пуста.</span><span class="sxs-lookup"><span data-stu-id="680c6-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="680c6-816">Этот код находится в конструкторе и выполняется каждый раз при обнаружении нового HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="680c6-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="680c6-817">Если вы удалите все элементы, конструктор создаст `Item1` при следующем вызове метода API.</span><span class="sxs-lookup"><span data-stu-id="680c6-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="680c6-818">Поэтому может создаться впечатление, что удаление не было выполнено, хотя это не так.</span><span class="sxs-lookup"><span data-stu-id="680c6-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="680c6-819">2\.1. Добавление методов Get</span><span class="sxs-lookup"><span data-stu-id="680c6-819">Add Get methods 2.1</span></span>

<span data-ttu-id="680c6-820">Чтобы получить API, который извлекает элементы списка дел, добавьте следующие методы в класс `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="680c6-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="680c6-821">Эти методы реализуют две конечные точки GET:</span><span class="sxs-lookup"><span data-stu-id="680c6-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="680c6-822">Если приложение все еще выполняется, оно останавливается.</span><span class="sxs-lookup"><span data-stu-id="680c6-822">Stop the app if it's still running.</span></span> <span data-ttu-id="680c6-823">Затем оно запускается снова с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="680c6-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="680c6-824">Протестируйте приложение, вызвав эти две конечные точки в браузере.</span><span class="sxs-lookup"><span data-stu-id="680c6-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="680c6-825">Пример:</span><span class="sxs-lookup"><span data-stu-id="680c6-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="680c6-826">При вызове `GetTodoItems` возвращается следующий ответ HTTP:</span><span class="sxs-lookup"><span data-stu-id="680c6-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="680c6-827">2\.1. Маршрутизация и пути URL</span><span class="sxs-lookup"><span data-stu-id="680c6-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="680c6-828">Атрибут [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) обозначает метод, который отвечает на запрос HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="680c6-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="680c6-829">Путь URL для каждого метода формируется следующим образом:</span><span class="sxs-lookup"><span data-stu-id="680c6-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="680c6-830">Возьмите строку шаблона в атрибуте `Route` контроллера:</span><span class="sxs-lookup"><span data-stu-id="680c6-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="680c6-831">Замените `[controller]` именем контроллера (по соглашению это имя класса контроллера без суффикса "Controller").</span><span class="sxs-lookup"><span data-stu-id="680c6-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="680c6-832">В этом примере класс контроллера носит имя **Todo**, а сам контроллер, соответственно, — "todo".</span><span class="sxs-lookup"><span data-stu-id="680c6-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="680c6-833">В ASP.NET Core [маршрутизация](xref:mvc/controllers/routing) реализуется без учета регистра символов.</span><span class="sxs-lookup"><span data-stu-id="680c6-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="680c6-834">Если атрибут `[HttpGet]` имеет шаблон маршрута (например, `[HttpGet("products")]`), добавьте его к пути.</span><span class="sxs-lookup"><span data-stu-id="680c6-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="680c6-835">В этом примере шаблон не используется.</span><span class="sxs-lookup"><span data-stu-id="680c6-835">This sample doesn't use a template.</span></span> <span data-ttu-id="680c6-836">Дополнительные сведения см. в разделе [Маршрутизация атрибутов с помощью атрибутов Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="680c6-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="680c6-837">В следующем методе `GetTodoItem``"{id}"` — это переменная-заполнитель для уникального идентификатора элемента задачи.</span><span class="sxs-lookup"><span data-stu-id="680c6-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="680c6-838">При вызове `GetTodoItem` параметру метода `id` присваивается значение `"{id}"` в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="680c6-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="680c6-839">2\.1. Возвращаемые значения</span><span class="sxs-lookup"><span data-stu-id="680c6-839">Return values 2.1</span></span>

<span data-ttu-id="680c6-840">Возвращаемый тип методов `GetTodoItems` и `GetTodoItem` — [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="680c6-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="680c6-841">ASP.NET Core автоматически сериализует объект в формат [JSON](https://www.json.org/) и записывает данные JSON в тело сообщения ответа.</span><span class="sxs-lookup"><span data-stu-id="680c6-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="680c6-842">Код ответа для этого типа возвращаемого значения равен 200, что свидетельствует об отсутствии необработанных исключений.</span><span class="sxs-lookup"><span data-stu-id="680c6-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="680c6-843">Необработанные исключения преобразуются в ошибки 5xx.</span><span class="sxs-lookup"><span data-stu-id="680c6-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="680c6-844">Типы возвращаемых значений `ActionResult` могут представлять широкий спектр кодов состояний HTTP.</span><span class="sxs-lookup"><span data-stu-id="680c6-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="680c6-845">Например, метод `GetTodoItem` может возвращать два разных значения состояния:</span><span class="sxs-lookup"><span data-stu-id="680c6-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="680c6-846">Если запрошенному идентификатору не соответствует ни один элемент, метод возвращает ошибку 404 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>).</span><span class="sxs-lookup"><span data-stu-id="680c6-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="680c6-847">В противном случае метод возвращает код 200 с телом ответа JSON.</span><span class="sxs-lookup"><span data-stu-id="680c6-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="680c6-848">При возвращении `item` возвращается ответ HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="680c6-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="680c6-849">2\.1. Тестирование метода GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="680c6-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="680c6-850">В этом учебнике для тестирования веб-API используется Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="680c6-851">Установите [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="680c6-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="680c6-852">Запустите веб-приложение.</span><span class="sxs-lookup"><span data-stu-id="680c6-852">Start the web app.</span></span>
* <span data-ttu-id="680c6-853">Запустите Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-853">Start Postman.</span></span>
* <span data-ttu-id="680c6-854">Отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="680c6-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="680c6-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="680c6-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="680c6-856">В меню **Файл** > **Параметры** (вкладка **Общие**), отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="680c6-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="680c6-857">Visual Studio Code/Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="680c6-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="680c6-858">В меню **Postman** > **Настройки** (вкладка **Общие**) отключите параметр **Проверка SSL-сертификата**.</span><span class="sxs-lookup"><span data-stu-id="680c6-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="680c6-859">Кроме того, можно выбрать значок гаечного ключа и выбрать **Параметры**, а затем отключить проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="680c6-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="680c6-860">После тестирования контроллера снова включите проверку SSL-сертификата.</span><span class="sxs-lookup"><span data-stu-id="680c6-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="680c6-861">Создайте новый запрос.</span><span class="sxs-lookup"><span data-stu-id="680c6-861">Create a new request.</span></span>
  * <span data-ttu-id="680c6-862">Укажите метод HTTP **GET**.</span><span class="sxs-lookup"><span data-stu-id="680c6-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="680c6-863">Задайте для URI запроса значение `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="680c6-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="680c6-864">Например, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="680c6-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="680c6-865">Выберите режим **Представление с двумя областями** в Postman.</span><span class="sxs-lookup"><span data-stu-id="680c6-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="680c6-866">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-866">Select **Send**.</span></span>

![Postman с запросом Get](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="680c6-868">2\.1. Добавление метода Create</span><span class="sxs-lookup"><span data-stu-id="680c6-868">Add a Create method 2.1</span></span>

<span data-ttu-id="680c6-869">Добавьте следующий метод `PostTodoItem` в *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="680c6-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="680c6-870">Предыдущий код является методом HTTP POST, обозначенным атрибутом [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="680c6-871">Этот метод получает значение элемента списка дел из текста HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="680c6-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="680c6-872">Метод `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="680c6-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="680c6-873">В случае успеха возвращает код состояния HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="680c6-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="680c6-874">HTTP 201 представляет собой стандартный ответ для метода HTTP POST, создающий ресурс на сервере.</span><span class="sxs-lookup"><span data-stu-id="680c6-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="680c6-875">Добавляет заголовок `Location` в ответ.</span><span class="sxs-lookup"><span data-stu-id="680c6-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="680c6-876">Заголовок `Location` расположения указывает URI вновь созданной задачи.</span><span class="sxs-lookup"><span data-stu-id="680c6-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="680c6-877">Дополнительные сведения см. в статье [10.2.2 201 "Создан ресурс"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="680c6-878">Указывает действие `GetTodoItem` для создания URI заголовка `Location`.</span><span class="sxs-lookup"><span data-stu-id="680c6-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="680c6-879">Ключевое слово `nameof` C# используется для предотвращения жесткого программирования имени действия в вызове `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="680c6-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="680c6-880">2\.1. Тестирование метода PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="680c6-881">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-881">Build the project.</span></span>
* <span data-ttu-id="680c6-882">В Postman укажите метод HTTP `POST`.</span><span class="sxs-lookup"><span data-stu-id="680c6-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="680c6-883">Задайте для URI значение `https://localhost:<port>/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="680c6-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="680c6-884">Например, `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="680c6-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="680c6-885">Откройте вкладку **Тело**.</span><span class="sxs-lookup"><span data-stu-id="680c6-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="680c6-886">Установите переключатель **без обработки**.</span><span class="sxs-lookup"><span data-stu-id="680c6-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="680c6-887">Задайте тип **JSON (приложение/json)** .</span><span class="sxs-lookup"><span data-stu-id="680c6-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="680c6-888">В теле запроса введите код JSON для элемента списка дел:</span><span class="sxs-lookup"><span data-stu-id="680c6-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="680c6-889">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-889">Select **Send**.</span></span>

  ![Postman с запросом Create](first-web-api/_static/create.png)

  <span data-ttu-id="680c6-891">Если вы получаете ошибку 405 "Недопустимый метод", это может свидетельствовать о том, что после добавления метода `PostTodoItem` не была выполнена компиляция проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="680c6-892">2\.1. Тестирование URI заголовка расположения</span><span class="sxs-lookup"><span data-stu-id="680c6-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="680c6-893">Перейдите на вкладку **Заголовки** в области **Ответ**.</span><span class="sxs-lookup"><span data-stu-id="680c6-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="680c6-894">Скопируйте значение заголовка **Расположение**:</span><span class="sxs-lookup"><span data-stu-id="680c6-894">Copy the **Location** header value:</span></span>

  ![Вкладка "Заголовки" в консоли Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="680c6-896">Укажите метод GET.</span><span class="sxs-lookup"><span data-stu-id="680c6-896">Set the method to GET.</span></span>
* <span data-ttu-id="680c6-897">Задайте для URI значение `https://localhost:<port>/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="680c6-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="680c6-898">Например, `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="680c6-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="680c6-899">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="680c6-900">2\.1. Добавление метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="680c6-901">Добавьте приведенный ниже метод `PutTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="680c6-902">Страница `PutTodoItem` аналогична странице `PostTodoItem`, но использует запрос HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="680c6-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="680c6-903">Ответ — [204 (Нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="680c6-904">Согласно спецификации HTTP, запрос PUT требует, чтобы клиент отправлял всю обновленную сущность, а не только изменения.</span><span class="sxs-lookup"><span data-stu-id="680c6-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="680c6-905">Чтобы обеспечить поддержку частичных обновлений, используйте [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="680c6-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="680c6-906">Если возникнет ошибка вызова `PutTodoItem`, вызовите `GET`, чтобы в базе данных был один элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="680c6-907">2\.1. Тестирование метода PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="680c6-908">В этом примере используется база данных в памяти, которая должна быть инициирована при каждом запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="680c6-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="680c6-909">При выполнении вызова PUT в базе данных уже должен существовать какой-либо элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="680c6-910">Для этого перед вызовом PUT выполните вызов GET, чтобы убедиться в наличии такого элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="680c6-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="680c6-911">Обновите элемент списка дел с идентификатором 1 и присвойте ему имя "feed fish":</span><span class="sxs-lookup"><span data-stu-id="680c6-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="680c6-912">На следующем рисунке показан процесс обновления Postman:</span><span class="sxs-lookup"><span data-stu-id="680c6-912">The following image shows the Postman update:</span></span>

![Консоль Postman с ответом 204 (Нет содержимого)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="680c6-914">2\.1. Добавление метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="680c6-915">Добавьте приведенный ниже метод `DeleteTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="680c6-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="680c6-916">`DeleteTodoItem`Ответ[ — 204 (нет содержимого)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="680c6-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="680c6-917">2\.1. Тестирование метода DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="680c6-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="680c6-918">Удалите элемент списка дел с помощью Postman:</span><span class="sxs-lookup"><span data-stu-id="680c6-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="680c6-919">Укажите метод `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="680c6-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="680c6-920">Укажите URI удаляемого объекта (например, `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="680c6-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="680c6-921">Нажмите кнопку **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="680c6-921">Select **Send**.</span></span>

<span data-ttu-id="680c6-922">В этом примере приложения вы можете удалить все элементы.</span><span class="sxs-lookup"><span data-stu-id="680c6-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="680c6-923">Однако в случае удаления последнего элемента в момент следующего вызова API конструктор класса модели создаст новый элемент.</span><span class="sxs-lookup"><span data-stu-id="680c6-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="680c6-924">2\.1. Вызов веб-API с помощью JavaScript</span><span class="sxs-lookup"><span data-stu-id="680c6-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="680c6-925">В этом разделе описано, как добавить HTML-страницу, которая использует JavaScript для вызова веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="680c6-926">jQuery инициирует запрос.</span><span class="sxs-lookup"><span data-stu-id="680c6-926">jQuery initiates the request.</span></span> <span data-ttu-id="680c6-927">JavaScript изменяет страницу, используя сведения из ответа API.</span><span class="sxs-lookup"><span data-stu-id="680c6-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="680c6-928">Настройте приложение для [обслуживания статических файлов](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) и [включения сопоставления файлов по умолчанию](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A), обновив *Startup.cs* следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="680c6-929">Создайте папку *wwwroot* в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="680c6-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="680c6-930">Добавьте HTML-файл *index.html* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="680c6-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="680c6-931">Замените его содержимое следующей разметкой:</span><span class="sxs-lookup"><span data-stu-id="680c6-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="680c6-932">Добавьте файл JavaScript с именем *site.js* в каталог *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="680c6-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="680c6-933">Замените его содержимое следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="680c6-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="680c6-934">Может потребоваться изменение параметров запуска проекта ASP.NET Core для локального тестирования HTML-страницы:</span><span class="sxs-lookup"><span data-stu-id="680c6-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="680c6-935">Откройте файл *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="680c6-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="680c6-936">Удалите свойство `launchUrl`, чтобы приложение открылось через *index.html* &mdash; файл проекта по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="680c6-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="680c6-937">В этом примере вызываются все методы CRUD в веб-API.</span><span class="sxs-lookup"><span data-stu-id="680c6-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="680c6-938">Ниже приводится пояснение вызовов API.</span><span class="sxs-lookup"><span data-stu-id="680c6-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="680c6-939">2\.1. Получение списка элементов задач</span><span class="sxs-lookup"><span data-stu-id="680c6-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="680c6-940">jQuery отправляет запрос HTTP GET к веб-API, который возвращает ответ JSON, представляющий массив элементов списка дел.</span><span class="sxs-lookup"><span data-stu-id="680c6-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="680c6-941">В случае успешного запроса используется функция обратного вызова `success`.</span><span class="sxs-lookup"><span data-stu-id="680c6-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="680c6-942">При обратном вызове в модель DOM вносятся данные о задачах.</span><span class="sxs-lookup"><span data-stu-id="680c6-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="680c6-943">2\.1. Добавление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="680c6-944">jQuery отправляет запрос HTTP POST с элементом списка дел в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="680c6-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="680c6-945">Для параметров `accepts` и `contentType` указывается `application/json`, чтобы указать тип носителя при получении и отправке.</span><span class="sxs-lookup"><span data-stu-id="680c6-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="680c6-946">Элемент списка дел преобразуется в JSON с помощью [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="680c6-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="680c6-947">Если интерфейс API возвращает код состояния успешного выполнения, вызывается функция `getData` для обновления HTML-таблицы.</span><span class="sxs-lookup"><span data-stu-id="680c6-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="680c6-948">2\.1. Обновление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="680c6-949">Обновление элемента списка дел выполняется аналогично его добавлению.</span><span class="sxs-lookup"><span data-stu-id="680c6-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="680c6-950">`url` изменяется, чтобы добавить уникальный идентификатор для элемента, а в качестве `type` устанавливается `PUT`.</span><span class="sxs-lookup"><span data-stu-id="680c6-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="680c6-951">2\.1. Удаление элемента задачи</span><span class="sxs-lookup"><span data-stu-id="680c6-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="680c6-952">Чтобы удалить элемент списка дел, установите для `type` в вызове AJAX значение `DELETE` и укажите уникальный идентификатор элемента в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="680c6-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="680c6-953">2\.1. Добавление поддержки аутентификации в веб-API</span><span class="sxs-lookup"><span data-stu-id="680c6-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="680c6-954">2\.1. Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="680c6-954">Additional resources 2.1</span></span>

<span data-ttu-id="680c6-955">[Просмотреть или скачать пример кода для этого учебника](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="680c6-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="680c6-956">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="680c6-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="680c6-957">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="680c6-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="680c6-958">Версия руководства на YouTube</span><span class="sxs-lookup"><span data-stu-id="680c6-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
