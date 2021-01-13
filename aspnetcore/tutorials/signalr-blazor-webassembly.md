---
title: Использование SignalR для ASP.NET Core с размещенным приложением Blazor WebAssembly
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797357"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="abd5e-103">Использование SignalR для ASP.NET Core с размещенным приложением Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="abd5e-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="abd5e-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="abd5e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="abd5e-105">В этом руководстве описаны основы того, как с помощью SignalR и Blazor WebAssembly создать приложение, работающее в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="abd5e-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="abd5e-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="abd5e-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="abd5e-107">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="abd5e-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="abd5e-108">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="abd5e-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="abd5e-109">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="abd5e-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="abd5e-110">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="abd5e-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="abd5e-111">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="abd5e-111">Add Razor component code for chat</span></span>

<span data-ttu-id="abd5e-112">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="abd5e-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="abd5e-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="abd5e-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="abd5e-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="abd5e-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="abd5e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abd5e-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="abd5e-116">[Visual Studio 2019 16.8 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="abd5e-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="abd5e-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abd5e-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="abd5e-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="abd5e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="abd5e-119">Visual Studio для Mac 8.8 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="abd5e-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="abd5e-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="abd5e-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="abd5e-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abd5e-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="abd5e-122">[Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="abd5e-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="abd5e-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abd5e-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="abd5e-124">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="abd5e-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="abd5e-125">Visual Studio для Mac 8.6 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="abd5e-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="abd5e-126">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="abd5e-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="abd5e-127">Создание проекта размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="abd5e-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="abd5e-128">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="abd5e-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="abd5e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abd5e-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="abd5e-130">Требуются Visual Studio 16.8 или более поздней версии и пакет SDK для .NET Core 5.0.0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="abd5e-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="abd5e-131">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="abd5e-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="abd5e-132">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="abd5e-132">Create a new project.</span></span>

1. <span data-ttu-id="abd5e-133">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="abd5e-134">Введите `BlazorSignalRApp` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="abd5e-135">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="abd5e-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="abd5e-136">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-136">Select **Create**.</span></span>

1. <span data-ttu-id="abd5e-137">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="abd5e-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="abd5e-138">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="abd5e-139">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="abd5e-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abd5e-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="abd5e-141">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abd5e-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="abd5e-142">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="abd5e-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="abd5e-143">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="abd5e-144">Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="abd5e-145">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="abd5e-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="abd5e-146">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="abd5e-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="abd5e-147">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="abd5e-148">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="abd5e-149">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="abd5e-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="abd5e-150">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-150">Select **Next**.</span></span>

1. <span data-ttu-id="abd5e-151">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="abd5e-152">Установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="abd5e-153">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-153">Select **Next**.</span></span>

1. <span data-ttu-id="abd5e-154">В поле **Имя проекта** присвойте имя приложению `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="abd5e-155">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-155">Select **Create**.</span></span>

   <span data-ttu-id="abd5e-156">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="abd5e-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="abd5e-157">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="abd5e-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="abd5e-158">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="abd5e-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="abd5e-159">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="abd5e-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="abd5e-160">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abd5e-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="abd5e-161">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="abd5e-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="abd5e-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abd5e-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="abd5e-163">В **обозревателе решений** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="abd5e-164">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="abd5e-165">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="abd5e-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="abd5e-166">В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и нажмите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="abd5e-167">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="abd5e-168">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="abd5e-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="abd5e-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abd5e-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="abd5e-170">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="abd5e-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="abd5e-171">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="abd5e-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="abd5e-172">На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="abd5e-173">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="abd5e-174">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="abd5e-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="abd5e-175">В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="abd5e-176">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="abd5e-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="abd5e-177">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="abd5e-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="abd5e-178">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="abd5e-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="abd5e-179">Добавление пакета System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="abd5e-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="abd5e-180">Из-за проблемы с разрешением пакета при использовании [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 в приложении ASP.NET Core 3,1 для проекта `BlazorSignalRApp.Server` требуется ссылка на пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="abd5e-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="abd5e-181">Проблема будет устранена в будущем выпуске .NET 5 с исправлениями.</span><span class="sxs-lookup"><span data-stu-id="abd5e-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="abd5e-182">См. дополнительные сведения о том, как [System.Text.Json определяет netcoreapp3.0 без зависимостей (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="abd5e-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="abd5e-183">Чтобы добавить [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) в проект `BlazorSignalRApp.Server` размещенного решения Blazor ASP.NET Core 3.1, следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="abd5e-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="abd5e-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abd5e-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="abd5e-185">В **обозревателе решений** щелкните проект `BlazorSignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="abd5e-186">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="abd5e-187">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="abd5e-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="abd5e-188">В результатах поиска выберите пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) и нажмите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="abd5e-189">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="abd5e-190">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="abd5e-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="abd5e-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abd5e-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="abd5e-192">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="abd5e-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="abd5e-193">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="abd5e-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="abd5e-194">На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="abd5e-195">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="abd5e-196">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="abd5e-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="abd5e-197">В результатах поиска установите флажок рядом с пакетом [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="abd5e-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="abd5e-198">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="abd5e-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="abd5e-199">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="abd5e-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="abd5e-200">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="abd5e-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="abd5e-201">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="abd5e-201">Add a SignalR hub</span></span>

<span data-ttu-id="abd5e-202">В проекте `BlazorSignalRApp.Server` создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="abd5e-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="abd5e-203">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="abd5e-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="abd5e-204">В проекте `BlazorSignalRApp.Server` откройте файл `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="abd5e-205">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="abd5e-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="abd5e-206">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="abd5e-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="abd5e-207">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="abd5e-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="abd5e-208">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="abd5e-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="abd5e-209">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="abd5e-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="abd5e-210">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="abd5e-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="abd5e-211">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="abd5e-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="abd5e-212">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="abd5e-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="abd5e-213">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="abd5e-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="abd5e-214">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="abd5e-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="abd5e-215">В проекте `BlazorSignalRApp.Client` откройте файл `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="abd5e-216">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="abd5e-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="abd5e-217">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="abd5e-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="abd5e-218">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="abd5e-218">Run the app</span></span>

<span data-ttu-id="abd5e-219">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="abd5e-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="abd5e-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="abd5e-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="abd5e-221">В **обозревателе решений** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="abd5e-222">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="abd5e-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="abd5e-223">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="abd5e-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="abd5e-224">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="abd5e-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="abd5e-225">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="abd5e-225">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="abd5e-227">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="abd5e-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="abd5e-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="abd5e-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="abd5e-229">Когда VS Code предлагает создать профиль запуска для серверного приложения (`.vscode/launch.json`), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="abd5e-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="abd5e-230">Когда VS Code предлагает создать профиль запуска для серверного приложения (`.vscode/launch.json`), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="abd5e-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="abd5e-231">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="abd5e-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="abd5e-232">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="abd5e-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="abd5e-233">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="abd5e-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="abd5e-234">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="abd5e-234">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="abd5e-236">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="abd5e-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="abd5e-237">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="abd5e-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="abd5e-238">На боковой панели **Решение** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="abd5e-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="abd5e-239">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="abd5e-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="abd5e-240">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="abd5e-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="abd5e-241">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="abd5e-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="abd5e-242">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="abd5e-242">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="abd5e-244">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="abd5e-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="abd5e-245">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="abd5e-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="abd5e-246">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="abd5e-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="abd5e-247">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="abd5e-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="abd5e-248">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="abd5e-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="abd5e-249">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="abd5e-249">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="abd5e-251">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="abd5e-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="abd5e-252">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="abd5e-252">Next steps</span></span>

<span data-ttu-id="abd5e-253">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="abd5e-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="abd5e-254">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="abd5e-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="abd5e-255">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="abd5e-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="abd5e-256">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="abd5e-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="abd5e-257">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="abd5e-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="abd5e-258">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="abd5e-258">Add Razor component code for chat</span></span>

<span data-ttu-id="abd5e-259">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="abd5e-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="abd5e-260"><xref:blazor/index>
> [Проверка подлинности маркера носителя с помощью сервера Identity, WebSockets и отправляемыми сервером событиями](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="abd5e-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="abd5e-261">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="abd5e-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="abd5e-262">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="abd5e-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
