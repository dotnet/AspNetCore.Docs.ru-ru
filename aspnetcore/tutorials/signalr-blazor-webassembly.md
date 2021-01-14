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
ms.openlocfilehash: 2f5630eac65b880bdefff2a4baf4f1878e981536
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058393"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="26970-103">Использование SignalR для ASP.NET Core с размещенным приложением Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="26970-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="26970-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26970-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="26970-105">В этом руководстве описаны основы того, как с помощью SignalR и Blazor WebAssembly создать приложение, работающее в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="26970-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="26970-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="26970-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26970-107">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="26970-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="26970-108">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="26970-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="26970-109">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="26970-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="26970-110">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="26970-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="26970-111">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="26970-111">Add Razor component code for chat</span></span>

<span data-ttu-id="26970-112">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="26970-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="26970-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26970-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26970-114">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="26970-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="26970-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26970-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26970-116">[Visual Studio 2019 16.8 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="26970-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="26970-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26970-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26970-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="26970-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="26970-119">Visual Studio для Mac 8.8 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="26970-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="26970-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="26970-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="26970-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26970-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="26970-122">[Visual Studio 2019 16.6 или более поздней версии](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**</span><span class="sxs-lookup"><span data-stu-id="26970-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="26970-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26970-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26970-124">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="26970-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="26970-125">Visual Studio для Mac 8.6 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="26970-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="26970-126">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="26970-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="26970-127">Создание проекта размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="26970-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="26970-128">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="26970-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26970-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26970-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26970-130">Требуются Visual Studio 16.8 или более поздней версии и пакет SDK для .NET Core 5.0.0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="26970-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26970-131">Требуются Visual Studio 16.6 или более поздней версии и пакет SDK для .NET Core 3.1.300 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="26970-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="26970-132">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="26970-132">Create a new project.</span></span>

1. <span data-ttu-id="26970-133">Выберите **Приложение Blazor** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="26970-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="26970-134">Введите `BlazorSignalRApp` в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="26970-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="26970-135">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="26970-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="26970-136">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="26970-136">Select **Create**.</span></span>

1. <span data-ttu-id="26970-137">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="26970-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="26970-138">В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="26970-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="26970-139">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="26970-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26970-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26970-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="26970-141">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="26970-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="26970-142">Откройте папку проекта приложения в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="26970-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="26970-143">Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="26970-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="26970-144">Visual Studio Code автоматически добавляет папку `.vscode` с созданными файлами `launch.json` и `tasks.json`.</span><span class="sxs-lookup"><span data-stu-id="26970-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26970-145">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="26970-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26970-146">Установите последнюю версию [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="26970-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="26970-147">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="26970-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="26970-148">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="26970-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="26970-149">Выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="26970-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="26970-150">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="26970-150">Select **Next**.</span></span>

1. <span data-ttu-id="26970-151">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="26970-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="26970-152">Установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="26970-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="26970-153">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="26970-153">Select **Next**.</span></span>

1. <span data-ttu-id="26970-154">В поле **Имя проекта** присвойте имя приложению `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="26970-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="26970-155">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="26970-155">Select **Create**.</span></span>

   <span data-ttu-id="26970-156">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="26970-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="26970-157">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="26970-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="26970-158">Откройте проект. Для этого перейдите к его папке и откройте файл решения проекта (`.sln`).</span><span class="sxs-lookup"><span data-stu-id="26970-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26970-159">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="26970-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26970-160">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="26970-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="26970-161">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="26970-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26970-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26970-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="26970-163">В **обозревателе решений** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26970-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26970-164">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="26970-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26970-165">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="26970-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="26970-166">В результатах поиска выберите пакет [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и нажмите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="26970-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="26970-167">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="26970-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="26970-168">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="26970-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26970-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26970-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="26970-170">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="26970-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26970-171">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="26970-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26970-172">На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Client` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26970-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26970-173">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="26970-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26970-174">Нажав кнопку **Обзор**, введите `Microsoft.AspNetCore.SignalR.Client` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="26970-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="26970-175">В результатах поиска установите флажок рядом с пакетом [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="26970-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="26970-176">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="26970-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26970-177">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="26970-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26970-178">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="26970-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="26970-179">Добавление пакета System.Text.Encodings.Web</span><span class="sxs-lookup"><span data-stu-id="26970-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="26970-180">Из-за проблемы с разрешением пакета при использовании [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 в приложении ASP.NET Core 3,1 для проекта `BlazorSignalRApp.Server` требуется ссылка на пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="26970-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="26970-181">Проблема будет устранена в будущем выпуске .NET 5 с исправлениями.</span><span class="sxs-lookup"><span data-stu-id="26970-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="26970-182">См. дополнительные сведения о том, как [System.Text.Json определяет netcoreapp3.0 без зависимостей (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="26970-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="26970-183">Чтобы добавить [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) в проект `BlazorSignalRApp.Server` размещенного решения Blazor ASP.NET Core 3.1, следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="26970-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26970-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26970-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="26970-185">В **обозревателе решений** щелкните проект `BlazorSignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26970-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26970-186">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="26970-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26970-187">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="26970-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="26970-188">В результатах поиска выберите пакет [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) и нажмите **Установить**.</span><span class="sxs-lookup"><span data-stu-id="26970-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="26970-189">Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="26970-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="26970-190">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.</span><span class="sxs-lookup"><span data-stu-id="26970-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26970-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26970-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="26970-192">Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="26970-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26970-193">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="26970-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26970-194">На боковой панели **Решение** щелкните проект `BlazorSignalRApp.Server` правой кнопкой мыши и выберите пункт **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="26970-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="26970-195">Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение `nuget.org`.</span><span class="sxs-lookup"><span data-stu-id="26970-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="26970-196">Нажав кнопку **Обзор**, введите `System.Text.Encodings.Web` в поле поиска.</span><span class="sxs-lookup"><span data-stu-id="26970-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="26970-197">В результатах поиска установите флажок рядом с пакетом [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) и выберите **Добавить пакет**.</span><span class="sxs-lookup"><span data-stu-id="26970-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="26970-198">Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.</span><span class="sxs-lookup"><span data-stu-id="26970-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26970-199">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="26970-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="26970-200">В командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="26970-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="26970-201">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="26970-201">Add a SignalR hub</span></span>

<span data-ttu-id="26970-202">В проекте `BlazorSignalRApp.Server` создайте папку `Hubs` (plural) и добавьте следующий класс `ChatHub` (`Hubs/ChatHub.cs`):</span><span class="sxs-lookup"><span data-stu-id="26970-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="26970-203">Добавление служб и конечной точки для концентратора SignalR</span><span class="sxs-lookup"><span data-stu-id="26970-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="26970-204">В проекте `BlazorSignalRApp.Server` откройте файл `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="26970-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="26970-205">Добавьте пространство имен для класса `ChatHub` в начало файла:</span><span class="sxs-lookup"><span data-stu-id="26970-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26970-206">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="26970-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="26970-207">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="26970-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="26970-208">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="26970-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="26970-209">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="26970-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26970-210">Добавьте службы SignalR и промежуточного ПО для сжатия ответа в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="26970-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="26970-211">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="26970-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="26970-212">Используйте ПО промежуточного слоя для сжатия ответа в верхней части конфигурации конвейера обработки.</span><span class="sxs-lookup"><span data-stu-id="26970-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="26970-213">Между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора.</span><span class="sxs-lookup"><span data-stu-id="26970-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="26970-214">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="26970-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="26970-215">В проекте `BlazorSignalRApp.Client` откройте файл `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="26970-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26970-216">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="26970-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26970-217">Замените разметку следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="26970-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="26970-218">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="26970-218">Run the app</span></span>

<span data-ttu-id="26970-219">Следуйте указаниям по выбору инструментов:</span><span class="sxs-lookup"><span data-stu-id="26970-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="26970-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26970-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="26970-221">В **обозревателе решений** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="26970-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="26970-222">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="26970-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26970-223">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="26970-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26970-224">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="26970-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26970-225">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="26970-225">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="26970-227">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26970-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="26970-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="26970-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="26970-229">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.</span><span class="sxs-lookup"><span data-stu-id="26970-229">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26970-230">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="26970-230">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26970-231">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="26970-231">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26970-232">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="26970-232">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="26970-234">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26970-234">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="26970-235">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="26970-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26970-236">На боковой панели **Решение** выберите проект `BlazorSignalRApp.Server`.</span><span class="sxs-lookup"><span data-stu-id="26970-236">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="26970-237">Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="26970-237">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="26970-238">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="26970-238">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26970-239">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="26970-239">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26970-240">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="26970-240">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="26970-242">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26970-242">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="26970-243">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="26970-243">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="26970-244">В командной оболочке выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="26970-244">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="26970-245">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="26970-245">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="26970-246">Выберите любой браузер, введите имя и сообщение и нажмите кнопку для отправки сообщения.</span><span class="sxs-lookup"><span data-stu-id="26970-246">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="26970-247">Имя и сообщение отображаются на обеих страницах мгновенно:</span><span class="sxs-lookup"><span data-stu-id="26970-247">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly Пример приложения в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="26970-249">Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="26970-249">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="26970-250">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="26970-250">Next steps</span></span>

<span data-ttu-id="26970-251">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="26970-251">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26970-252">создавать проект размещенного приложения Blazor WebAssembly;</span><span class="sxs-lookup"><span data-stu-id="26970-252">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="26970-253">Добавление клиентской библиотеки SignalR</span><span class="sxs-lookup"><span data-stu-id="26970-253">Add the SignalR client library</span></span>
> * <span data-ttu-id="26970-254">добавлять концентратор SignalR;</span><span class="sxs-lookup"><span data-stu-id="26970-254">Add a SignalR hub</span></span>
> * <span data-ttu-id="26970-255">добавлять службы и конечную точку SignalR для концентратора SignalR;</span><span class="sxs-lookup"><span data-stu-id="26970-255">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="26970-256">добавлять код компонента Razor для чата.</span><span class="sxs-lookup"><span data-stu-id="26970-256">Add Razor component code for chat</span></span>

<span data-ttu-id="26970-257">Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:</span><span class="sxs-lookup"><span data-stu-id="26970-257">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="26970-258"><xref:blazor/index>
> [Проверка подлинности маркера носителя с помощью сервера Identity, WebSockets и отправляемыми сервером событиями](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="26970-258"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="26970-259">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="26970-259">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="26970-260">Согласование независимо от источника для проверки подлинности для SignalR</span><span class="sxs-lookup"><span data-stu-id="26970-260">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
