---
title: Использование SignalR для ASP.NET Core с Blazor
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e9bb79eb5bd8f56fe8014981de76e210eb35c2de
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589534"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="d9f7a-103">Использование SignalR для ASP.NET Core с Blazor</span><span class="sxs-lookup"><span data-stu-id="d9f7a-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="d9f7a-104">В этом руководстве описаны основы того, как с помощью SignalR и Blazor создать приложение, работающее в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="d9f7a-105">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d9f7a-106">создавать проекты Blazor;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-106">Create a Blazor project</span></span>
> * <span data-ttu-id="d9f7a-107">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="d9f7a-108">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="d9f7a-109">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d9f7a-110">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-110">Add Razor component code for chat</span></span>

<span data-ttu-id="d9f7a-111">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="d9f7a-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d9f7a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9f7a-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="d9f7a-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9f7a-115">[Visual Studio 2019 16.8 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d9f7a-118">Visual Studio для Mac 8.8 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d9f7a-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-119">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d9f7a-121">[Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="d9f7a-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-123">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d9f7a-124">Visual Studio для Mac 8.6 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d9f7a-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-125">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="d9f7a-126">Создание размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d9f7a-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="d9f7a-127">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d9f7a-129">Требуются Visual Studio 16.8 или более поздней версии и пакет SDK для .NET Core 5.0.0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d9f7a-130">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="d9f7a-131">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-131">Create a new project.</span></span>

1. <span data-ttu-id="d9f7a-132">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d9f7a-133">Введите `BlazorWebAssemblySignalRApp` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d9f7a-134">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d9f7a-135">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-135">Select **Create**.</span></span>

1. <span data-ttu-id="d9f7a-136">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="d9f7a-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="d9f7a-137">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="d9f7a-138">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d9f7a-140">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="d9f7a-141">Параметр `-ho|--hosted` создает размещенное решение Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="d9f7a-142">Сведения о настройке ресурсов VS Code в папке `.vscode` см. в руководстве по операционной системе **Linux** в <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="d9f7a-143">Параметр `-o|--output` создает папку для решения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="d9f7a-144">Если вы создали папку для решения и она открыта в оболочке командной строки, не указывайте параметр `-o|--output` и значение для создания решения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="d9f7a-145">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d9f7a-146">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d9f7a-147">Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-148">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-149">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d9f7a-150">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d9f7a-151">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d9f7a-152">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="d9f7a-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d9f7a-153">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-153">Select **Next**.</span></span>

1. <span data-ttu-id="d9f7a-154">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d9f7a-155">Установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="d9f7a-156">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-156">Select **Next**.</span></span>

1. <span data-ttu-id="d9f7a-157">В поле **Имя проекта** присвойте имя приложению `BlazorWebAssemblySignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="d9f7a-158">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-158">Select **Create**.</span></span>

   <span data-ttu-id="d9f7a-159">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d9f7a-160">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d9f7a-161">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-162">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d9f7a-163">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="d9f7a-164">Параметр `-ho|--hosted` создает размещенное решение Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="d9f7a-165">Параметр `-o|--output` создает папку для решения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="d9f7a-166">Если вы создали папку для решения и она открыта в оболочке командной строки, не указывайте параметр `-o|--output` и значение для создания решения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d9f7a-167">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d9f7a-169">В **обозревателе решений** щелкните проект `BlazorWebAssemblySignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-170">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-171">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-172">В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d9f7a-173">Задайте версию в соответствии с общей платформой приложения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d9f7a-174">Выберите пункт **Установить**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-174">Select **Install**.</span></span>

1. <span data-ttu-id="d9f7a-175">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d9f7a-176">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d9f7a-178">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d9f7a-179">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-180">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-181">На боковой панели **Решение** щелкните проект `BlazorWebAssemblySignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-182">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-183">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-184">В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d9f7a-185">Задайте версию в соответствии с общей платформой приложения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d9f7a-186">Выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="d9f7a-187">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-188">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d9f7a-189">В оболочке командной строки из папки решения выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d9f7a-190">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="d9f7a-191">Добавление пакета System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="d9f7a-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="d9f7a-192">*Сведения в данном разделе применяются только к приложениям ASP.NET Core версии 3.x.*</span><span class="sxs-lookup"><span data-stu-id="d9f7a-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="d9f7a-193">Из-за проблемы с разрешением пакета при использовании [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x в приложении ASP.NET Core 3.x для проекта `BlazorWebAssemblySignalRApp.Server` требуется ссылка на пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="d9f7a-194">Проблема будет устранена в будущем выпуске .NET 5 с исправлениями.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="d9f7a-195">См. дополнительные сведения о том, как [System.Text.Json определяет netcoreapp3.0 без зависимостей (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="d9f7a-196">Чтобы добавить [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) в проект `BlazorWebAssemblySignalRApp.Server` размещенного решения Blazor ASP.NET Core 3.1, следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d9f7a-198">В **обозревателе решений** щелкните проект `BlazorWebAssemblySignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-199">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-200">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-201">В результатах поиска выберите пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="d9f7a-202">Выберите версию пакета, соответствующую используемой общей платформе.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="d9f7a-203">Выберите пункт **Установить**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-203">Select **Install**.</span></span>

1. <span data-ttu-id="d9f7a-204">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d9f7a-205">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d9f7a-207">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="d9f7a-208">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-209">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-210">На боковой панели **Решение** щелкните проект `BlazorWebAssemblySignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-211">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-212">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-213">В результатах поиска установите флажок рядом с пакетом [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), выберите правильную версию пакета, соответствующую используемой общей платформе, и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="d9f7a-214">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-215">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d9f7a-216">В оболочке командной строки из папки решения выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="d9f7a-217">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d9f7a-218">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-218">Add a SignalR hub</span></span>

<span data-ttu-id="d9f7a-219">В проекте `BlazorWebAssemblySignalRApp.Server` создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="d9f7a-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d9f7a-220">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d9f7a-221">В проекте `BlazorWebAssemblySignalRApp.Server` откройте файл `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d9f7a-222">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-223">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="d9f7a-224">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d9f7a-225">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d9f7a-226">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-227">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="d9f7a-228">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d9f7a-229">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d9f7a-230">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d9f7a-231">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d9f7a-232">В проекте `BlazorWebAssemblySignalRApp.Client` откройте файл `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-233">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-233">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-234">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-234">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="d9f7a-235">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="d9f7a-235">Run the app</span></span>

<span data-ttu-id="d9f7a-236">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d9f7a-238">В **обозревателе решений** выберите проект `BlazorWebAssemblySignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="d9f7a-239">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d9f7a-240">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-241">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-242">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-242">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-244">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d9f7a-246">Сведения о настройке ресурсов VS Code в папке `.vscode` см. в руководстве по операционной системе **Linux** в <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="d9f7a-247">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d9f7a-248">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-249">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-250">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-250">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-252">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-253">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-254">На боковой панели **Решение** выберите проект `BlazorWebAssemblySignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="d9f7a-255">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d9f7a-256">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-257">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-258">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-258">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-260">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-261">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d9f7a-262">В оболочке командной строки из папки решения выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="d9f7a-263">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-264">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-265">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-265">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-267">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="d9f7a-268">Создание приложения Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d9f7a-268">Create a Blazor Server app</span></span>

<span data-ttu-id="d9f7a-269">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d9f7a-271">Требуются Visual Studio 16.8 или более поздней версии и пакет SDK для .NET Core 5.0.0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="d9f7a-272">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="d9f7a-273">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-273">Create a new project.</span></span>

1. <span data-ttu-id="d9f7a-274">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="d9f7a-275">Введите `BlazorServerSignalRApp` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="d9f7a-276">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d9f7a-277">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-277">Select **Create**.</span></span>

1. <span data-ttu-id="d9f7a-278">Выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="d9f7a-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="d9f7a-279">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d9f7a-281">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="d9f7a-282">Параметр `-o|--output` создает папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="d9f7a-283">Если вы создали папку для проекта и она открыта в оболочке командной строки, не указывайте параметр `-o|--output` и значение для создания проекта.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="d9f7a-284">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="d9f7a-285">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="d9f7a-286">Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="d9f7a-287">Сведения о настройке ресурсов VS Code в папке `.vscode` см. в руководстве по операционной системе **Linux** в <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-288">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-289">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="d9f7a-290">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d9f7a-291">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="d9f7a-292">Выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="d9f7a-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d9f7a-293">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-293">Select **Next**.</span></span>

1. <span data-ttu-id="d9f7a-294">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="d9f7a-295">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-295">Select **Next**.</span></span>

1. <span data-ttu-id="d9f7a-296">В поле **Имя проекта** присвойте имя приложению `BlazorServerSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="d9f7a-297">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-297">Select **Create**.</span></span>

   <span data-ttu-id="d9f7a-298">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d9f7a-299">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="d9f7a-300">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-301">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d9f7a-302">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="d9f7a-303">Параметр `-o|--output` создает папку для проекта.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="d9f7a-304">Если вы создали папку для проекта и она открыта в оболочке командной строки, не указывайте параметр `-o|--output` и значение для создания проекта.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="d9f7a-305">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d9f7a-307">В **обозревателе решений** щелкните проект `BlazorServerSignalRApp` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-308">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-309">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-310">В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d9f7a-311">Задайте версию в соответствии с общей платформой приложения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d9f7a-312">Выберите пункт **Установить**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-312">Select **Install**.</span></span>

1. <span data-ttu-id="d9f7a-313">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d9f7a-314">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d9f7a-316">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d9f7a-317">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-318">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-319">На боковой панели **Решение** щелкните проект `BlazorServerSignalRApp` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-320">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-321">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-322">В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="d9f7a-323">Задайте версию в соответствии с общей платформой приложения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="d9f7a-324">Выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="d9f7a-325">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-326">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d9f7a-327">Выполните следующую команду в папке проекта в командной строке.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="d9f7a-328">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="d9f7a-329">Добавление пакета System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="d9f7a-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="d9f7a-330">*Сведения в данном разделе применяются только к приложениям ASP.NET Core версии 3.x.*</span><span class="sxs-lookup"><span data-stu-id="d9f7a-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="d9f7a-331">Из-за проблемы с разрешением пакета при использовании [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x в приложении ASP.NET Core 3.x для проекта требуется ссылка на пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="d9f7a-332">Проблема будет устранена в будущем выпуске .NET 5 с исправлениями.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="d9f7a-333">См. дополнительные сведения о том, как [System.Text.Json определяет netcoreapp3.0 без зависимостей (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="d9f7a-334">Чтобы добавить [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) в проект, следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="d9f7a-336">В **обозревателе решений** щелкните проект `BlazorServerSignalRApp` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-337">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-338">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-339">В результатах поиска выберите пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="d9f7a-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="d9f7a-340">Выберите версию пакета, соответствующую используемой общей платформе.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="d9f7a-341">Выберите пункт **Установить**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-341">Select **Install**.</span></span>

1. <span data-ttu-id="d9f7a-342">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="d9f7a-343">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="d9f7a-345">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="d9f7a-346">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-347">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-348">На боковой панели **Решение** щелкните проект `BlazorServerSignalRApp` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="d9f7a-349">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="d9f7a-350">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="d9f7a-351">В результатах поиска установите флажок рядом с пакетом [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), выберите правильную версию пакета, соответствующую используемой общей платформе, и нажмите кнопку **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="d9f7a-352">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-353">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="d9f7a-354">Выполните следующую команду в папке проекта в командной строке.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="d9f7a-355">Чтобы добавить более раннюю версию пакета, укажите параметр `--version {VERSION}`, в котором заполнитель `{VERSION}` является версией добавляемого пакета.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="d9f7a-356">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-356">Add a SignalR hub</span></span>

<span data-ttu-id="d9f7a-357">Создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="d9f7a-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="d9f7a-358">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="d9f7a-359">Откройте файл `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="d9f7a-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="d9f7a-360">Добавьте пространства имен для <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> и класс `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-361">Добавьте службы промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="d9f7a-362">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d9f7a-363">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d9f7a-364">Между конечными точками для сопоставления концентратора Blazor и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-365">Добавьте службы промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="d9f7a-366">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="d9f7a-367">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="d9f7a-368">Между конечными точками для сопоставления концентратора Blazor и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="d9f7a-369">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="d9f7a-370">Откройте файл `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="d9f7a-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-371">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-371">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="d9f7a-372">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-372">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="d9f7a-373">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="d9f7a-373">Run the app</span></span>

<span data-ttu-id="d9f7a-374">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d9f7a-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9f7a-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d9f7a-376">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d9f7a-377">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-378">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-379">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-379">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-381">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d9f7a-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9f7a-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d9f7a-383">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d9f7a-384">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-385">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-386">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-386">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-388">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d9f7a-389">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="d9f7a-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d9f7a-390">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="d9f7a-391">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-392">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-393">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-393">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-395">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d9f7a-396">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9f7a-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d9f7a-397">Выполните следующие команды в папке проекта в командной строке.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="d9f7a-398">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="d9f7a-399">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="d9f7a-400">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-400">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="d9f7a-402">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="d9f7a-403">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="d9f7a-403">Next steps</span></span>

<span data-ttu-id="d9f7a-404">В этом руководстве вы узнали, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d9f7a-405">создавать проекты Blazor;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-405">Create a Blazor project</span></span>
> * <span data-ttu-id="d9f7a-406">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="d9f7a-407">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="d9f7a-408">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="d9f7a-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="d9f7a-409">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="d9f7a-409">Add Razor component code for chat</span></span>

<span data-ttu-id="d9f7a-410">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="d9f7a-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="d9f7a-411"><xref:blazor/index>
> [Проверка подлинности маркера носителя с помощью сервера Identity, WebSockets и отправляемыми сервером событиями](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="d9f7a-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9f7a-412">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d9f7a-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="d9f7a-413">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="d9f7a-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
