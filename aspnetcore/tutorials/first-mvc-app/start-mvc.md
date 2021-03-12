---
title: Начало работы с MVC ASP.NET Core
author: rick-anderson
description: Сведения о начале работы с MVC ASP.NET Core.
ms.author: riande
ms.date: 01/20/2021
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
uid: tutorials/first-mvc-app/start-mvc
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8e5f216a354b1ed7559b433d3a4867627bf60df3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589781"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="eec8a-103">Начало работы с MVC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eec8a-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="eec8a-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="eec8a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="eec8a-105">Это первое руководство из серии материалов по веб-разработке MVC ASP.NET Core с использованием контроллеров и представлений.</span><span class="sxs-lookup"><span data-stu-id="eec8a-105">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="eec8a-106">Пройдя всю серию, вы создадите приложение, которое управляет базой данных фильмов и отображает ее.</span><span class="sxs-lookup"><span data-stu-id="eec8a-106">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="eec8a-107">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="eec8a-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eec8a-108">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="eec8a-108">Create a web app.</span></span>
> * <span data-ttu-id="eec8a-109">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="eec8a-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="eec8a-110">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="eec8a-110">Work with a database.</span></span>
> * <span data-ttu-id="eec8a-111">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="eec8a-111">Add search and validation.</span></span>

<span data-ttu-id="eec8a-112">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eec8a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eec8a-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="eec8a-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eec8a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eec8a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eec8a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eec8a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eec8a-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="eec8a-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="eec8a-117">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="eec8a-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eec8a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eec8a-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eec8a-119">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="eec8a-119">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="eec8a-120">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
* <span data-ttu-id="eec8a-121">В диалоговом окне **Настроить новый проект** введите `MvcMovie` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="eec8a-122">Важно присвоить проекту имя *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="eec8a-122">It's important to name the project *MvcMovie*.</span></span> <span data-ttu-id="eec8a-123">Регистр символов должен соответствовать каждому из совпадений `namespace` при копировании кода.</span><span class="sxs-lookup"><span data-stu-id="eec8a-123">Capitalization needs to match each `namespace` matches when code is copied.</span></span>
* <span data-ttu-id="eec8a-124">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-124">Select **Create**.</span></span>
* <span data-ttu-id="eec8a-125">В диалоговом окне **Создайте веб-приложение ASP.NET Core** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="eec8a-125">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
  * <span data-ttu-id="eec8a-126">В раскрывающихся списках выберите **.NET Core** и **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-126">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
  * <span data-ttu-id="eec8a-127">Щелкните **ASP.NET Core Web App (Model-View-Controller)** (Веб-приложение ASP.NET Core (модель — представление — контроллер)).</span><span class="sxs-lookup"><span data-stu-id="eec8a-127">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
  * <span data-ttu-id="eec8a-128">**Create** (создать).</span><span class="sxs-lookup"><span data-stu-id="eec8a-128">**Create**.</span></span>

![<span data-ttu-id="eec8a-129">Создание веб-приложения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eec8a-129">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="eec8a-130">Альтернативные подходы к созданию проекта см. в статье [Создание проекта в Visual Studio](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="eec8a-130">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="eec8a-131">В Visual Studio используется шаблон проекта по умолчанию для созданного проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="eec8a-131">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="eec8a-132">Созданный проект это:</span><span class="sxs-lookup"><span data-stu-id="eec8a-132">The created project:</span></span>

* <span data-ttu-id="eec8a-133">рабочее приложение;</span><span class="sxs-lookup"><span data-stu-id="eec8a-133">Is a working app.</span></span>
* <span data-ttu-id="eec8a-134">простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="eec8a-134">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eec8a-135">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eec8a-135">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eec8a-136">В этом руководстве предполагается, что вы умеете работать с VS Code.</span><span class="sxs-lookup"><span data-stu-id="eec8a-136">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="eec8a-137">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="eec8a-137">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="eec8a-138">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eec8a-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="eec8a-139">Перейдите в каталог `cd`, в котором будет находиться проект.</span><span class="sxs-lookup"><span data-stu-id="eec8a-139">Change to the directory (`cd`) that will contain the project.</span></span>
* <span data-ttu-id="eec8a-140">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eec8a-140">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="eec8a-141">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-141">If a dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**</span></span>

  * <span data-ttu-id="eec8a-142">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="eec8a-142">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="eec8a-143">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eec8a-143">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eec8a-144">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="eec8a-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eec8a-145">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-145">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="eec8a-147">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-147">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="eec8a-148">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-148">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="eec8a-150">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="eec8a-150">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="eec8a-151">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-151">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="eec8a-152">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 5.x.</span><span class="sxs-lookup"><span data-stu-id="eec8a-152">If an option to select a **Target Framework** is presented, select the latest 5.x version.</span></span>
  * <span data-ttu-id="eec8a-153">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-153">Select **Next**.</span></span>

* <span data-ttu-id="eec8a-154">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-154">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="eec8a-156">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="eec8a-156">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eec8a-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eec8a-157">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eec8a-158">Нажмите клавиши CTRL+F5, чтобы запустить приложение без отладчика.</span><span class="sxs-lookup"><span data-stu-id="eec8a-158">Select Ctrl+F5 to run the app without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="eec8a-159">Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="eec8a-159">Visual Studio:</span></span>

  * <span data-ttu-id="eec8a-160">запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview);</span><span class="sxs-lookup"><span data-stu-id="eec8a-160">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="eec8a-161">запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="eec8a-161">Runs the app.</span></span>

  <span data-ttu-id="eec8a-162">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-162">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eec8a-163">Стандартное имя узла для локального компьютера — `localhost`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-163">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="eec8a-164">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="eec8a-164">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="eec8a-165">Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:</span><span class="sxs-lookup"><span data-stu-id="eec8a-165">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="eec8a-166">Внесите изменения в код.</span><span class="sxs-lookup"><span data-stu-id="eec8a-166">Make code changes.</span></span>
* <span data-ttu-id="eec8a-167">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="eec8a-167">Save the file.</span></span>
* <span data-ttu-id="eec8a-168">Быстро обновить браузер и просмотреть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="eec8a-168">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="eec8a-169">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="eec8a-169">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Меню отладки](start-mvc/_static/debug_menu50.png)

<span data-ttu-id="eec8a-171">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-171">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express50.png)

<span data-ttu-id="eec8a-173">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="eec8a-173">The following image shows the app:</span></span>

![Домашняя или индексная страница](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="eec8a-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eec8a-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eec8a-176">Нажмите клавиши CTRL+F5 чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="eec8a-176">Select Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="eec8a-177">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="eec8a-177">Visual Studio Code:</span></span>

  * <span data-ttu-id="eec8a-178">запускает [Kestrel](xref:fundamentals/servers/kestrel);</span><span class="sxs-lookup"><span data-stu-id="eec8a-178">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="eec8a-179">запускает браузер;</span><span class="sxs-lookup"><span data-stu-id="eec8a-179">Launches a browser.</span></span>
  * <span data-ttu-id="eec8a-180">перенаправляет по адресу `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-180">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="eec8a-181">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-181">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="eec8a-182">Стандартное имя узла для локального компьютера — `localhost`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-182">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="eec8a-183">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="eec8a-183">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="eec8a-184">Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:</span><span class="sxs-lookup"><span data-stu-id="eec8a-184">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="eec8a-185">Внесите изменения в код.</span><span class="sxs-lookup"><span data-stu-id="eec8a-185">Make code changes.</span></span>
* <span data-ttu-id="eec8a-186">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="eec8a-186">Save the file.</span></span>
* <span data-ttu-id="eec8a-187">Быстро обновить браузер и просмотреть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="eec8a-187">Quickly refresh the browser and see the code changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eec8a-189">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="eec8a-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eec8a-190">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="eec8a-190">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="eec8a-191">Visual Studio для Mac:</span><span class="sxs-lookup"><span data-stu-id="eec8a-191">Visual Studio for Mac:</span></span>

  * <span data-ttu-id="eec8a-192">запускает сервер [Kestrel](xref:fundamentals/servers/index#kestrel);</span><span class="sxs-lookup"><span data-stu-id="eec8a-192">Starts [Kestrel](xref:fundamentals/servers/index#kestrel) server.</span></span>
  * <span data-ttu-id="eec8a-193">запускает браузер;</span><span class="sxs-lookup"><span data-stu-id="eec8a-193">Launches a browser.</span></span>
  * <span data-ttu-id="eec8a-194">перенаправляет по адресу `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="eec8a-194">Navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

  [!INCLUDE[](~/includes/trustCertMac.md)]

  <span data-ttu-id="eec8a-195">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-195">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eec8a-196">Стандартное имя узла для локального компьютера — `localhost`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-196">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="eec8a-197">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="eec8a-197">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="eec8a-198">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="eec8a-198">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="eec8a-199">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="eec8a-199">The following image shows the app:</span></span>

![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="eec8a-201">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="eec8a-201">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="eec8a-202">Далее: Добавление контроллера</span><span class="sxs-lookup"><span data-stu-id="eec8a-202">Next: Add a controller</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="eec8a-203">Это первое руководство из серии материалов по веб-разработке MVC ASP.NET Core с использованием контроллеров и представлений.</span><span class="sxs-lookup"><span data-stu-id="eec8a-203">This is the first tutorial of a series that teaches ASP.NET Core MVC web development with controllers and views.</span></span>

<span data-ttu-id="eec8a-204">Пройдя всю серию, вы создадите приложение, которое управляет базой данных фильмов и отображает ее.</span><span class="sxs-lookup"><span data-stu-id="eec8a-204">At the end of the series, you'll have an app that manages and displays movie data.</span></span> <span data-ttu-id="eec8a-205">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="eec8a-205">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eec8a-206">Создание веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="eec8a-206">Create a web app.</span></span>
> * <span data-ttu-id="eec8a-207">Добавление модели и формирование шаблона.</span><span class="sxs-lookup"><span data-stu-id="eec8a-207">Add and scaffold a model.</span></span>
> * <span data-ttu-id="eec8a-208">Работа с базой данных.</span><span class="sxs-lookup"><span data-stu-id="eec8a-208">Work with a database.</span></span>
> * <span data-ttu-id="eec8a-209">Добавление поиска и проверки.</span><span class="sxs-lookup"><span data-stu-id="eec8a-209">Add search and validation.</span></span>

<span data-ttu-id="eec8a-210">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eec8a-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mvc-app/start-mvc/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eec8a-211">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="eec8a-211">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eec8a-212">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eec8a-212">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eec8a-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eec8a-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eec8a-214">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="eec8a-214">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="eec8a-215">Создание веб-приложения</span><span class="sxs-lookup"><span data-stu-id="eec8a-215">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eec8a-216">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eec8a-216">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eec8a-217">В Visual Studio выберите **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-217">From the Visual Studio, select **Create a new project**.</span></span>

* <span data-ttu-id="eec8a-218">Выберите **Веб-приложение ASP.NET Core** > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-218">Select **ASP.NET Core Web Application** > **Next**.</span></span>

  ![Создание нового проекта веб-приложения ASP.NET Core](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="eec8a-220">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-220">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="eec8a-221">Имя **MvcMovie** необходимо присвоить для того, чтобы при копировании кода пространства имен совпали.</span><span class="sxs-lookup"><span data-stu-id="eec8a-221">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Настройка нового проекта](start-mvc/_static/config.png)

* <span data-ttu-id="eec8a-223">Выберите **Веб-приложение (модель — представление — контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="eec8a-223">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="eec8a-224">В раскрывающихся списках выберите пункты **.NET Core** и **ASP.NET Core 3.1**, а затем щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-224">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

  ![<span data-ttu-id="eec8a-225">Диалоговое окно нового проекта, .NET Core в левой области, веб-узел ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eec8a-225">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="eec8a-226">В Visual Studio используется шаблон проекта по умолчанию для созданного проекта MVC.</span><span class="sxs-lookup"><span data-stu-id="eec8a-226">Visual Studio used the default project template for the created MVC project.</span></span> <span data-ttu-id="eec8a-227">Созданный проект это:</span><span class="sxs-lookup"><span data-stu-id="eec8a-227">The created project:</span></span>

* <span data-ttu-id="eec8a-228">рабочее приложение;</span><span class="sxs-lookup"><span data-stu-id="eec8a-228">Is a working app.</span></span>
* <span data-ttu-id="eec8a-229">простой начальный проект.</span><span class="sxs-lookup"><span data-stu-id="eec8a-229">Is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eec8a-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eec8a-230">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eec8a-231">В этом руководстве предполагается, что вы умеете работать с VS Code.</span><span class="sxs-lookup"><span data-stu-id="eec8a-231">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="eec8a-232">Дополнительные сведения см. в разделах [Начало работы с VS Code](https://code.visualstudio.com/docs) и [Справка по Visual Studio Code](#visual-studio-code-help).</span><span class="sxs-lookup"><span data-stu-id="eec8a-232">For more information, see [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help).</span></span>

* <span data-ttu-id="eec8a-233">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="eec8a-233">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="eec8a-234">Измените каталог `cd` на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="eec8a-234">Change directories (`cd`) to a folder that will contain the project.</span></span>
* <span data-ttu-id="eec8a-235">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="eec8a-235">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="eec8a-236">Появится диалоговое окно с предупреждением **В MvcMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-236">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**, select **Yes**.</span></span>

  * <span data-ttu-id="eec8a-237">`dotnet new mvc -o MvcMovie`: создает новый проект MVC ASP.NET Core в папке *MvcMovie*.</span><span class="sxs-lookup"><span data-stu-id="eec8a-237">`dotnet new mvc -o MvcMovie`: Creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="eec8a-238">`code -r MvcMovie`. загружает файл проекта *MvcMovie.csproj* в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eec8a-238">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eec8a-239">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="eec8a-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eec8a-240">Щелкните **Файл** > **Новое решение**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-240">Select **File** > **New Solution**.</span></span>

  ![Новое решение macOS](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="eec8a-242">В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-242">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="eec8a-243">В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение (модель — представление — контроллер)**  > **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-243">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![Выбор шаблона веб-приложения macOS](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="eec8a-245">В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="eec8a-245">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="eec8a-246">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-246">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="eec8a-247">Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.</span><span class="sxs-lookup"><span data-stu-id="eec8a-247">If an option to select a **Target Framework** is presented, select the latest 3.x version.</span></span>
  * <span data-ttu-id="eec8a-248">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-248">Select **Next**.</span></span>

* <span data-ttu-id="eec8a-249">Присвойте проекту имя **MvcMovie** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-249">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![Имя проекта macOS](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="eec8a-251">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="eec8a-251">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eec8a-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eec8a-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eec8a-253">Нажмите клавиши CTRL+F5, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="eec8a-253">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="eec8a-254">Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eec8a-254">Visual Studio:</span></span>

  * <span data-ttu-id="eec8a-255">запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview);</span><span class="sxs-lookup"><span data-stu-id="eec8a-255">Starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview).</span></span>
  * <span data-ttu-id="eec8a-256">запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="eec8a-256">Runs the app.</span></span>

  <span data-ttu-id="eec8a-257">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-257">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eec8a-258">Стандартное имя узла для локального компьютера — `localhost`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-258">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="eec8a-259">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="eec8a-259">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

<span data-ttu-id="eec8a-260">Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:</span><span class="sxs-lookup"><span data-stu-id="eec8a-260">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="eec8a-261">Внесите изменения в код.</span><span class="sxs-lookup"><span data-stu-id="eec8a-261">Make code changes.</span></span>
* <span data-ttu-id="eec8a-262">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="eec8a-262">Save the file.</span></span>
* <span data-ttu-id="eec8a-263">Быстро обновить браузер и просмотреть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="eec8a-263">Quickly refresh the browser and see the code changes.</span></span>

<span data-ttu-id="eec8a-264">Из меню **Отладка** можно запустить приложение в режиме с отладкой или без.</span><span class="sxs-lookup"><span data-stu-id="eec8a-264">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

![Меню отладки](start-mvc/_static/debug_menu.png)

<span data-ttu-id="eec8a-266">Чтобы выполнить отладку приложения, нажмите кнопку **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="eec8a-266">You can debug the app by selecting the **IIS Express** button</span></span>

![IIS Express](start-mvc/_static/iis_express.png)

<span data-ttu-id="eec8a-268">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="eec8a-268">The following image shows the app:</span></span>

![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="eec8a-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eec8a-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eec8a-271">Нажмите клавиши CTRL+F5, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="eec8a-271">Select Ctrl+F5 to run the app without debugging.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="eec8a-272">Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="eec8a-272">Visual Studio Code:</span></span>

  * <span data-ttu-id="eec8a-273">запускает [Kestrel](xref:fundamentals/servers/kestrel);</span><span class="sxs-lookup"><span data-stu-id="eec8a-273">Starts [Kestrel](xref:fundamentals/servers/kestrel)</span></span>
  * <span data-ttu-id="eec8a-274">запускает браузер;</span><span class="sxs-lookup"><span data-stu-id="eec8a-274">Launches a browser.</span></span>
  * <span data-ttu-id="eec8a-275">перенаправляет по адресу `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-275">Navigates to `https://localhost:5001`.</span></span>

  <span data-ttu-id="eec8a-276">В адресной строке указывается `localhost:port:5001`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-276">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="eec8a-277">Стандартное имя узла для локального компьютера — `localhost`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-277">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="eec8a-278">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="eec8a-278">Localhost only serves web requests from the local computer.</span></span>

<span data-ttu-id="eec8a-279">Запуск приложения без отладки путем нажатия клавиш CTRL+F5 позволяет:</span><span class="sxs-lookup"><span data-stu-id="eec8a-279">Launching the app without debugging by selecting Ctrl+F5 allows you to:</span></span>

* <span data-ttu-id="eec8a-280">Внесите изменения в код.</span><span class="sxs-lookup"><span data-stu-id="eec8a-280">Make code changes.</span></span>
* <span data-ttu-id="eec8a-281">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="eec8a-281">Save the file.</span></span>
* <span data-ttu-id="eec8a-282">Быстро обновить браузер и просмотреть изменения в коде.</span><span class="sxs-lookup"><span data-stu-id="eec8a-282">Quickly refresh the browser and see the code changes.</span></span>

  ![Домашняя или индексная страница](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eec8a-284">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="eec8a-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eec8a-285">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение.</span><span class="sxs-lookup"><span data-stu-id="eec8a-285">Select **Run** > **Start Without Debugging** to launch the app.</span></span>

  <span data-ttu-id="eec8a-286">Visual Studio для Mac: запускает сервер [Kestrel](xref:fundamentals/servers/index#kestrel), открывает браузер и перенаправляет по адресу `http://localhost:port`, где *port* — это номер порта, выбранный случайным образом.</span><span class="sxs-lookup"><span data-stu-id="eec8a-286">Visual Studio for Mac: starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<span data-ttu-id="eec8a-287">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-287">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="eec8a-288">Стандартное имя узла для локального компьютера — `localhost`.</span><span class="sxs-lookup"><span data-stu-id="eec8a-288">The standard hostname for your local computer is `localhost`.</span></span> <span data-ttu-id="eec8a-289">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="eec8a-289">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="eec8a-290">При запуске приложения вы увидите другой номер порта.</span><span class="sxs-lookup"><span data-stu-id="eec8a-290">When you run the app, you'll see a different port number.</span></span>

<span data-ttu-id="eec8a-291">В меню **Запуск** можно запустить приложение в режиме с отладкой или без нее.</span><span class="sxs-lookup"><span data-stu-id="eec8a-291">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

<span data-ttu-id="eec8a-292">Пример приложения приведен на следующем рисунке:</span><span class="sxs-lookup"><span data-stu-id="eec8a-292">The following image shows the app:</span></span>

![Домашняя или индексная страница](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="eec8a-294">В следующей части этого учебника мы поговорим об MVC и приступим к написанию кода.</span><span class="sxs-lookup"><span data-stu-id="eec8a-294">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="eec8a-295">Вперед</span><span class="sxs-lookup"><span data-stu-id="eec8a-295">Next</span></span>](adding-controller.md)

::: moniker-end
