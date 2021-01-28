---
title: Начало работы с SignalR ASP.NET Core
author: bradygaster
description: В этом учебнике рассказывается как создать приложение чата, которое использует SignalR для ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1c77648f809562389667da452bdbf3f25f67c558
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689322"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="12b07-103">Учебник. Начало работы с SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12b07-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="12b07-104">В этом учебнике описаны основы создания приложения, работающего в режиме реального времени, с помощью SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="12b07-105">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="12b07-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="12b07-106">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="12b07-106">Create a web project.</span></span>
> * <span data-ttu-id="12b07-107">добавлять клиентскую библиотеку SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="12b07-108">создавать концентратор SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="12b07-109">настраивать проект для использования SignalR;</span><span class="sxs-lookup"><span data-stu-id="12b07-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="12b07-110">Добавлять код для отправки сообщений из любого клиента всем подключенным клиентам.</span><span class="sxs-lookup"><span data-stu-id="12b07-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="12b07-111">В итоге вы получите работающее приложение чата:</span><span class="sxs-lookup"><span data-stu-id="12b07-111">At the end, you'll have a working chat app:</span></span>

![SignalR Пример приложения](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="12b07-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="12b07-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="12b07-117">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="12b07-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-118">Visual Studio</span></span>](#tab/visual-studio/)

  * <span data-ttu-id="12b07-119">В меню выберите **Файл > Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="12b07-119">From the menu, select **File > New Project**.</span></span>
  * <span data-ttu-id="12b07-120">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="12b07-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>
  * <span data-ttu-id="12b07-121">В диалоговом окне **Настроить новый проект** укажите имя проекта *SignalRChat*, а затем выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="12b07-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>
  * <span data-ttu-id="12b07-122">В диалоговом окне **Создание веб-приложения ASP.NET Core** выберите платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="12b07-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1**.</span></span>
  * <span data-ttu-id="12b07-123">Выберите **Веб-приложение**, чтобы создать проект, который использует Razor Pages, и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="12b07-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

  * <span data-ttu-id="12b07-126">Откройте [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) и перейдите в папку, в которой будет создаваться папка нового проекта.</span><span class="sxs-lookup"><span data-stu-id="12b07-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>
  * <span data-ttu-id="12b07-127">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="12b07-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-128">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  * <span data-ttu-id="12b07-129">В меню выберите **Файл > Создать решение**.</span><span class="sxs-lookup"><span data-stu-id="12b07-129">From the menu, select **File > New Solution**.</span></span>
  * <span data-ttu-id="12b07-130">Выберите **.NET Core > Приложение > Веб-приложение** (не выбирайте **Веб-приложение (модель — представление — контроллер)** ), а затем выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="12b07-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>
  * <span data-ttu-id="12b07-131">Убедитесь, что для параметра **Требуемая версия .NET Framework** указано **.NET Core 3.1**, а затем нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="12b07-131">Make sure the **Target Framework** is set to **.NET Core 3.1**, and then select **Next**.</span></span>
  * <span data-ttu-id="12b07-132">Присвойте проекту имя *SignalRChat* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="12b07-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="12b07-133">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-133">Add the SignalR client library</span></span>

<span data-ttu-id="12b07-134">Серверная библиотека SignalR входит в состав общей платформы ASP.NET Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="12b07-134">The SignalR server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="12b07-135">Клиентская библиотека JavaScript не добавляется в проект автоматически.</span><span class="sxs-lookup"><span data-stu-id="12b07-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="12b07-136">В рамках этого руководства вы будете использовать диспетчер библиотек (LibMan), чтобы получить клиентскую библиотеку из *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="12b07-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="12b07-137">unpkg — это сеть доставки содержимого, которая позволяет доставить любое содержимое из npm (диспетчера пакетов Node.js).</span><span class="sxs-lookup"><span data-stu-id="12b07-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-138">Visual Studio</span></span>](#tab/visual-studio/)

  * <span data-ttu-id="12b07-139">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Добавить** > **Клиентская библиотека**.</span><span class="sxs-lookup"><span data-stu-id="12b07-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>
  * <span data-ttu-id="12b07-140">В диалоговом окне **Add Client-Side Library** (Добавить клиентскую библиотеку) для параметра **Поставщик** выберите **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="12b07-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>
  * <span data-ttu-id="12b07-141">Для параметра **Библиотека** введите `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="12b07-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>
  * <span data-ttu-id="12b07-142">Щелкните **Choose specific files** (Выбрать определенные файлы), разверните папку *dist/browser* и выберите *signalr.js* и *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="12b07-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>
  * <span data-ttu-id="12b07-143">В поле **Целевое расположение** укажите *wwwroot/js/signalr/* и нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="12b07-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Диалоговое окно добавления клиентской библиотеки — выбор библиотеки](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="12b07-145">LibMan создает папку *wwwroot/js/signalr* и копирует в нее выбранные файлы.</span><span class="sxs-lookup"><span data-stu-id="12b07-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

  * <span data-ttu-id="12b07-147">В интегрированном терминале выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

  * <span data-ttu-id="12b07-148">Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="12b07-149">Возможно, придется подождать несколько секунд, прежде чем появятся выходные данные.</span><span class="sxs-lookup"><span data-stu-id="12b07-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="12b07-150">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="12b07-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="12b07-151">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="12b07-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="12b07-152">копирование файлов в назначение *wwwroot/js/signalr*;</span><span class="sxs-lookup"><span data-stu-id="12b07-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="12b07-153">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="12b07-153">Copy only the specified files.</span></span>

  <span data-ttu-id="12b07-154">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="12b07-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-155">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  * <span data-ttu-id="12b07-156">В **терминале** выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

  * <span data-ttu-id="12b07-157">Перейдите в папку проекта (где расположен файл *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="12b07-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

  * <span data-ttu-id="12b07-158">Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="12b07-159">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="12b07-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="12b07-160">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="12b07-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="12b07-161">копирование файлов в назначение *wwwroot/js/signalr*;</span><span class="sxs-lookup"><span data-stu-id="12b07-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="12b07-162">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="12b07-162">Copy only the specified files.</span></span>

  <span data-ttu-id="12b07-163">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="12b07-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="12b07-164">Создание концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-164">Create a SignalR hub</span></span>

<span data-ttu-id="12b07-165">*hub* — это класс, который служит в качестве конвейера высокого уровня для обработки взаимодействия между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="12b07-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

  * <span data-ttu-id="12b07-166">В папке проекта SignalRChat создайте папку *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="12b07-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>
  * <span data-ttu-id="12b07-167">В папке *Hubs* создайте файл *ChatHub.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="12b07-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="12b07-168">Класс `ChatHub` наследуется от класса SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="12b07-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="12b07-169">Класс `Hub` управляет подключениями, группами и обменом сообщениями.</span><span class="sxs-lookup"><span data-stu-id="12b07-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="12b07-170">Метод `SendMessage` может вызываться подключенным клиентом, чтобы отправить сообщение всем клиентам.</span><span class="sxs-lookup"><span data-stu-id="12b07-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="12b07-171">Далее в этом учебника показан клиентский код JavaScript, который вызывает метод.</span><span class="sxs-lookup"><span data-stu-id="12b07-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="12b07-172">Код SignalR является асинхронным, поэтому обеспечивает максимальную масштабируемость.</span><span class="sxs-lookup"><span data-stu-id="12b07-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="12b07-173">Настройка SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-173">Configure SignalR</span></span>

<span data-ttu-id="12b07-174">Сервер SignalR должен быть настроен для передачи запросов SignalR к SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="12b07-175">Добавьте следующий выделенный код в файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="12b07-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="12b07-176">В результате SignalR будет добавлен в системы внедрения зависимостей и маршрутизации ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12b07-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="12b07-177">Добавление клиентского кода SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-177">Add SignalR client code</span></span>

* <span data-ttu-id="12b07-178">Замените содержимое в файле *Pages\Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="12b07-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="12b07-179">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="12b07-179">The preceding code:</span></span>

  * <span data-ttu-id="12b07-180">Создает текстовые поля для имени и текста сообщения и кнопку отправки.</span><span class="sxs-lookup"><span data-stu-id="12b07-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="12b07-181">Создает список с `id="messagesList"` для отображения сообщений, полученных от концентратора SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="12b07-182">Содержит ссылки на скрипты для SignalR и код приложения *chat.js*, который создается в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="12b07-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="12b07-183">В папке *wwwroot/js* создайте файл *chat.js* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="12b07-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="12b07-184">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="12b07-184">The preceding code:</span></span>

  * <span data-ttu-id="12b07-185">Создает и запускает подключение.</span><span class="sxs-lookup"><span data-stu-id="12b07-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="12b07-186">Добавляет к кнопке отправки обработчик, который отправляет сообщения в концентратор.</span><span class="sxs-lookup"><span data-stu-id="12b07-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="12b07-187">Добавляет к объекту подключения обработчик, который получает сообщения из концентратора и добавляет их в список.</span><span class="sxs-lookup"><span data-stu-id="12b07-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="12b07-188">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="12b07-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="12b07-190">Нажмите клавиши **CTRL+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="12b07-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="12b07-192">В интегрированном терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="12b07-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-193">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  * <span data-ttu-id="12b07-194">В меню выберите **Запуск > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="12b07-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

  * <span data-ttu-id="12b07-195">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="12b07-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>
  * <span data-ttu-id="12b07-196">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить сообщение**.</span><span class="sxs-lookup"><span data-stu-id="12b07-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>
  <span data-ttu-id="12b07-197">Имя и сообщение отображаются на обеих страницах мгновенно.</span><span class="sxs-lookup"><span data-stu-id="12b07-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR Пример приложения](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="12b07-199">Если приложение не работает, откройте средства разработчика для браузера (F12) и перейдите в консоль.</span><span class="sxs-lookup"><span data-stu-id="12b07-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="12b07-200">Вы можете увидеть ошибки, связанные с вашим кодом HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12b07-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="12b07-201">Предположим, вы поместили *signalr.js* не в ту папку, которую указали.</span><span class="sxs-lookup"><span data-stu-id="12b07-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="12b07-202">В этом случае ссылка на этот файл не будет работать, и вы увидите сообщение об ошибке 404 в консоли.</span><span class="sxs-lookup"><span data-stu-id="12b07-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="12b07-203">![ошибка: signalr.js не найден](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="12b07-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="12b07-204">Если возникает ошибка ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY в Chrome, выполните эти команды, чтобы обновить сертификат разработки:</span><span class="sxs-lookup"><span data-stu-id="12b07-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="12b07-205">В этом учебнике описаны основы создания приложения, работающего в режиме реального времени, с помощью SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="12b07-206">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="12b07-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="12b07-207">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="12b07-207">Create a web project.</span></span>   
> * <span data-ttu-id="12b07-208">добавлять клиентскую библиотеку SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="12b07-209">создавать концентратор SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="12b07-210">настраивать проект для использования SignalR;</span><span class="sxs-lookup"><span data-stu-id="12b07-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="12b07-211">Добавлять код для отправки сообщений из любого клиента всем подключенным клиентам.</span><span class="sxs-lookup"><span data-stu-id="12b07-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="12b07-212">В итоге вы получите приложение для работы с чатом: ![SignalR Пример приложения](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="12b07-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="12b07-213">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="12b07-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-216">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="12b07-217">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="12b07-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="12b07-219">В меню выберите **Файл > Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="12b07-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="12b07-220">В диалоговом окне **Новый проект** выберите **Установленные > Visual C# > Веб > Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="12b07-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="12b07-221">Присвойте проекту имя *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="12b07-221">Name the project *SignalRChat*.</span></span>   

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="12b07-223">Выберите **Веб-приложение**, чтобы создать проект, который использует Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="12b07-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="12b07-224">Выберите целевую платформу **.NET Core**, **ASP.NET Core 2.2** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="12b07-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="12b07-227">Откройте [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) и перейдите в папку, в которой будет создаваться папка нового проекта.</span><span class="sxs-lookup"><span data-stu-id="12b07-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="12b07-228">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="12b07-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-229">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="12b07-230">В меню выберите **Файл > Создать решение**.</span><span class="sxs-lookup"><span data-stu-id="12b07-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="12b07-231">Выберите **.NET Core > Приложение > Веб-приложение ASP.NET Core** (не выбирайте **Веб-приложение ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="12b07-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="12b07-232">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="12b07-232">Select **Next**.</span></span>  

* <span data-ttu-id="12b07-233">Присвойте проекту имя *SignalRChat* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="12b07-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="12b07-234">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-234">Add the SignalR client library</span></span> 

<span data-ttu-id="12b07-235">Библиотека сервера SignalR входит в состав метапакета `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="12b07-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="12b07-236">Клиентская библиотека JavaScript не добавляется в проект автоматически.</span><span class="sxs-lookup"><span data-stu-id="12b07-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="12b07-237">В рамках этого руководства вы будете использовать диспетчер библиотек (LibMan), чтобы получить клиентскую библиотеку из *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="12b07-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="12b07-238">unpkg — это сеть доставки содержимого, которая позволяет доставить любое содержимое из npm (диспетчера пакетов Node.js).</span><span class="sxs-lookup"><span data-stu-id="12b07-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="12b07-240">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Добавить** > **Клиентская библиотека**.</span><span class="sxs-lookup"><span data-stu-id="12b07-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="12b07-241">В диалоговом окне **Add Client-Side Library** (Добавить клиентскую библиотеку) для параметра **Поставщик** выберите **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="12b07-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="12b07-242">В поле **Библиотека** введите `@microsoft/signalr@3` и выберите последнюю версию, но не предварительную.</span><span class="sxs-lookup"><span data-stu-id="12b07-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Диалоговое окно добавления клиентской библиотеки — выбор библиотеки](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="12b07-244">Щелкните **Choose specific files** (Выбрать определенные файлы), разверните папку *dist/browser* и выберите *signalr.js* и *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="12b07-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="12b07-245">В поле **Целевое расположение** укажите *wwwroot/lib/signalr/* и нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="12b07-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Диалоговое окно добавления клиентской библиотеки — выбор файлов и назначения](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="12b07-247">LibMan создает папку *wwwroot/lib/signalr* и копирует в нее выбранные файлы.</span><span class="sxs-lookup"><span data-stu-id="12b07-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="12b07-249">В интегрированном терминале выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="12b07-250">Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="12b07-251">Возможно, придется подождать несколько секунд, прежде чем появятся выходные данные.</span><span class="sxs-lookup"><span data-stu-id="12b07-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="12b07-252">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="12b07-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="12b07-253">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="12b07-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="12b07-254">копирование файлов в назначение *wwwroot/lib/signalr*;</span><span class="sxs-lookup"><span data-stu-id="12b07-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="12b07-255">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="12b07-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="12b07-256">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="12b07-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-257">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="12b07-258">В **терминале** выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="12b07-259">Перейдите в папку проекта (где расположен файл *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="12b07-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="12b07-260">Выполните команду ниже, чтобы получить клиентскую библиотеку SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="12b07-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="12b07-261">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="12b07-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="12b07-262">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="12b07-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="12b07-263">копирование файлов в назначение *wwwroot/lib/signalr*;</span><span class="sxs-lookup"><span data-stu-id="12b07-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="12b07-264">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="12b07-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="12b07-265">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="12b07-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="12b07-266">Создание концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-266">Create a SignalR hub</span></span>   

<span data-ttu-id="12b07-267">*hub* — это класс, который служит в качестве конвейера высокого уровня для обработки взаимодействия между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="12b07-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="12b07-268">В папке проекта SignalRChat создайте папку *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="12b07-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="12b07-269">В папке *Hubs* создайте файл *ChatHub.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="12b07-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="12b07-270">Класс `ChatHub` наследуется от класса SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="12b07-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="12b07-271">Класс `Hub` управляет подключениями, группами и обменом сообщениями.</span><span class="sxs-lookup"><span data-stu-id="12b07-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="12b07-272">Метод `SendMessage` может вызываться подключенным клиентом, чтобы отправить сообщение всем клиентам.</span><span class="sxs-lookup"><span data-stu-id="12b07-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="12b07-273">Далее в этом учебника показан клиентский код JavaScript, который вызывает метод.</span><span class="sxs-lookup"><span data-stu-id="12b07-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="12b07-274">Код SignalR является асинхронным, поэтому обеспечивает максимальную масштабируемость.</span><span class="sxs-lookup"><span data-stu-id="12b07-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="12b07-275">Настройка SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-275">Configure SignalR</span></span>  

<span data-ttu-id="12b07-276">Сервер SignalR должен быть настроен для передачи запросов SignalR к SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="12b07-277">Добавьте следующий выделенный код в файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="12b07-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="12b07-278">В результате SignalR будет добавлен в систему внедрения зависимостей ASP.NET Core и конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="12b07-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="12b07-279">Добавление клиентского кода SignalR</span><span class="sxs-lookup"><span data-stu-id="12b07-279">Add SignalR client code</span></span>    

* <span data-ttu-id="12b07-280">Замените содержимое в файле *Pages\Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="12b07-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="12b07-281">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="12b07-281">The preceding code:</span></span>   

  * <span data-ttu-id="12b07-282">Создает текстовые поля для имени и текста сообщения и кнопку отправки.</span><span class="sxs-lookup"><span data-stu-id="12b07-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="12b07-283">Создает список с `id="messagesList"` для отображения сообщений, полученных от концентратора SignalR.</span><span class="sxs-lookup"><span data-stu-id="12b07-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="12b07-284">Содержит ссылки на скрипты для SignalR и код приложения *chat.js*, который создается в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="12b07-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="12b07-285">В папке *wwwroot/js* создайте файл *chat.js* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="12b07-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="12b07-286">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="12b07-286">The preceding code:</span></span>   

  * <span data-ttu-id="12b07-287">Создает и запускает подключение.</span><span class="sxs-lookup"><span data-stu-id="12b07-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="12b07-288">Добавляет к кнопке отправки обработчик, который отправляет сообщения в концентратор.</span><span class="sxs-lookup"><span data-stu-id="12b07-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="12b07-289">Добавляет к объекту подключения обработчик, который получает сообщения из концентратора и добавляет их в список.</span><span class="sxs-lookup"><span data-stu-id="12b07-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="12b07-290">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="12b07-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="12b07-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12b07-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="12b07-292">Нажмите клавиши **CTRL+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="12b07-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="12b07-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12b07-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="12b07-294">В интегрированном терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="12b07-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12b07-295">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="12b07-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="12b07-296">В меню выберите **Запуск > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="12b07-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="12b07-297">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="12b07-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="12b07-298">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить сообщение**.</span><span class="sxs-lookup"><span data-stu-id="12b07-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="12b07-299">Имя и сообщение отображаются на обеих страницах мгновенно.</span><span class="sxs-lookup"><span data-stu-id="12b07-299">The name and message are displayed on both pages instantly.</span></span>   

  ![SignalR Пример приложения](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="12b07-301">Если приложение не работает, откройте средства разработчика для браузера (F12) и перейдите в консоль.</span><span class="sxs-lookup"><span data-stu-id="12b07-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="12b07-302">Вы можете увидеть ошибки, связанные с вашим кодом HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12b07-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="12b07-303">Предположим, вы поместили *signalr.js* не в ту папку, которую указали.</span><span class="sxs-lookup"><span data-stu-id="12b07-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="12b07-304">В этом случае ссылка на этот файл не будет работать, и вы увидите сообщение об ошибке 404 в консоли.</span><span class="sxs-lookup"><span data-stu-id="12b07-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="12b07-305">![ошибка: signalr.js не найден](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="12b07-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="12b07-306">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="12b07-306">Additional resources</span></span> 
* [<span data-ttu-id="12b07-307">Версия учебника на YouTube</span><span class="sxs-lookup"><span data-stu-id="12b07-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
