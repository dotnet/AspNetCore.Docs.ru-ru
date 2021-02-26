---
title: Инструменты для Blazor ASP.NET Core
author: guardrex
description: Сведения об инструментах, доступных для создания приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536250"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="02460-103">Инструменты для Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02460-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="02460-104">Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.</span><span class="sxs-lookup"><span data-stu-id="02460-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="02460-105">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="02460-105">Create a new project.</span></span>

1. <span data-ttu-id="02460-106">Выберите **Приложение Blazor** .</span><span class="sxs-lookup"><span data-stu-id="02460-106">Select **Blazor App**.</span></span> <span data-ttu-id="02460-107">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="02460-107">Select **Next**.</span></span>

1. <span data-ttu-id="02460-108">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02460-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="02460-109">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="02460-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="02460-110">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="02460-110">Select **Create**.</span></span>

1. <span data-ttu-id="02460-111">Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="02460-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="02460-112">Для работы с Blazor Server выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="02460-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="02460-113">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="02460-113">Select **Create**.</span></span>

   <span data-ttu-id="02460-114">Для работы с размещенной платформой Blazor WebAssembly установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="02460-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="02460-115">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="02460-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="02460-116">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="02460-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="02460-117">Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="02460-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="02460-118">При выполнении размещенного приложения Blazor WebAssembly запустите приложение из проекта **`Server`** решения.</span><span class="sxs-lookup"><span data-stu-id="02460-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="02460-119">Установите последнюю версию [пакета SDK для .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="02460-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="02460-120">Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="02460-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="02460-121">Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="02460-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="02460-122">Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="02460-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="02460-123">Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="02460-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="02460-124">Для работы с размещенной платформой Blazor WebAssembly добавьте параметр hosted (`-ho` или `--hosted`) в команду.</span><span class="sxs-lookup"><span data-stu-id="02460-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="02460-125">Для работы с Blazor Server выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="02460-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="02460-126">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="02460-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="02460-127">Откройте папку `WebApplication1` в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="02460-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="02460-128">Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта.</span><span class="sxs-lookup"><span data-stu-id="02460-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="02460-129">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="02460-129">Select **Yes**.</span></span>

   <span data-ttu-id="02460-130">**Запуск размещенного Blazor WebAssembly и конфигурация задачи**</span><span class="sxs-lookup"><span data-stu-id="02460-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="02460-131">Для размещенных решений Blazor WebAssembly добавьте (или переместите) папку`.vscode` с файлами `launch.json` и `tasks.json` в родительскую папку решения — папку, содержащую типичные имена папок проекта `Client`, `Server` и `Shared`.</span><span class="sxs-lookup"><span data-stu-id="02460-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="02460-132">Обновите или подтвердите, что конфигурация в файлах `launch.json` и `tasks.json` выполняет размещенное приложение Blazor WebAssembly из проекта **`Server`** .</span><span class="sxs-lookup"><span data-stu-id="02460-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="02460-133">**`.vscode/launch.json`** (конфигурация `launch`):</span><span class="sxs-lookup"><span data-stu-id="02460-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="02460-134">В предыдущей конфигурации для текущего рабочего каталога (`cwd`) заполнителем `{SERVER APP FOLDER}` является папка проекта **`Server`** , обычно "`Server`".</span><span class="sxs-lookup"><span data-stu-id="02460-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="02460-135">Если используется Microsoft Edge и в системе не установлен браузер Google Chrome, добавьте в конфигурацию дополнительное свойство `"browser": "edge"`.</span><span class="sxs-lookup"><span data-stu-id="02460-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="02460-136">Пример для папки проекта `Server`, которая порождает Microsoft Edge в качестве браузера для выполнения отладки, вместо браузера по умолчанию Google Chrome:</span><span class="sxs-lookup"><span data-stu-id="02460-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="02460-137">**`.vscode/tasks.json`** (аргументы [команды `dotnet`](/dotnet/core/tools/dotnet)):</span><span class="sxs-lookup"><span data-stu-id="02460-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="02460-138">В предыдущем аргументе:</span><span class="sxs-lookup"><span data-stu-id="02460-138">In the preceding argument:</span></span>

   * <span data-ttu-id="02460-139">Заполнитель `{SERVER APP FOLDER}` — это папка проекта **`Server`** , обычно "`Server`".</span><span class="sxs-lookup"><span data-stu-id="02460-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="02460-140">Заполнитель `{PROJECT NAME}` — это имя приложения, обычно основанное на имени решения, за которым следует "`.Server`" в приложении, созданном на основе шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="02460-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the Blazor project template.</span></span>

   <span data-ttu-id="02460-141">В следующем примере из [руководства по использованию SignalR с приложением Blazor WebAssembly](xref:tutorials/signalr-blazor) используется имя папки проекта `Server` и имя проекта `BlazorWebAssemblySignalRApp.Server`:</span><span class="sxs-lookup"><span data-stu-id="02460-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="02460-142">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="02460-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="02460-143">Установка доверия к сертификату разработки</span><span class="sxs-lookup"><span data-stu-id="02460-143">Trust a development certificate</span></span>

<span data-ttu-id="02460-144">В Linux отсутствует централизованный способ установки доверия к сертификату.</span><span class="sxs-lookup"><span data-stu-id="02460-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="02460-145">Обычно используется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="02460-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="02460-146">исключение URL-адреса приложения в списке исключений браузера;</span><span class="sxs-lookup"><span data-stu-id="02460-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="02460-147">установка доверия ко всем самозаверяющим сертификатам для `localhost`;</span><span class="sxs-lookup"><span data-stu-id="02460-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="02460-148">добавление сертификата в список доверенных сертификатов в браузере.</span><span class="sxs-lookup"><span data-stu-id="02460-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="02460-149">Дополнительные сведения см. в рекомендациях, предоставляемых для вашего браузера и дистрибутива Linux.</span><span class="sxs-lookup"><span data-stu-id="02460-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="02460-150">Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="02460-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="02460-151">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="02460-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="02460-152">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="02460-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="02460-153">Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="02460-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="02460-154">Для работы с Blazor Server выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="02460-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="02460-155">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="02460-155">Select **Next**.</span></span>

   <span data-ttu-id="02460-156">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="02460-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="02460-157">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="02460-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="02460-158">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="02460-158">Select **Next**.</span></span>

1. <span data-ttu-id="02460-159">Для работы с размещенной платформой Blazor WebAssembly установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="02460-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="02460-160">В поле **Имя проекта** присвойте имя приложению `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="02460-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="02460-161">Нажмите кнопку **создания**.</span><span class="sxs-lookup"><span data-stu-id="02460-161">Select **Create**.</span></span>

1. <span data-ttu-id="02460-162">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*.</span><span class="sxs-lookup"><span data-stu-id="02460-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="02460-163">Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.</span><span class="sxs-lookup"><span data-stu-id="02460-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="02460-164">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="02460-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="02460-165">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="02460-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="02460-166">Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="02460-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="02460-167">При выполнении размещенного приложения Blazor WebAssembly запустите приложение из проекта **`Server`** решения.</span><span class="sxs-lookup"><span data-stu-id="02460-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="02460-168">Использование Visual Studio для кроссплатформенной разработки на Blazor</span><span class="sxs-lookup"><span data-stu-id="02460-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="02460-169">[Visual Studio Code](https://code.visualstudio.com/) — это кроссплатформенная интегрированная среда разработки (IDE) с открытым кодом, которую можно использовать для разработки приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="02460-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="02460-170">Используйте .NET CLI для создания приложения Blazor, которое будет разрабатываться в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="02460-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="02460-171">Дополнительные сведения см. в [варианте этой статьи для Linux](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="02460-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="02460-172">Параметры шаблона Blazor</span><span class="sxs-lookup"><span data-stu-id="02460-172">Blazor template options</span></span>

<span data-ttu-id="02460-173">Платформа Blazor предоставляет шаблоны для создания приложений с использованием каждой из двух моделей размещения Blazor.</span><span class="sxs-lookup"><span data-stu-id="02460-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="02460-174">Шаблоны служат для создания проектов и решений Blazor независимо от того, какие средства выбраны для разработки на Blazor (Visual Studio, Visual Studio для Mac, Visual Studio Code или .NET CLI).</span><span class="sxs-lookup"><span data-stu-id="02460-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="02460-175">Шаблон проекта Blazor WebAssembly: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="02460-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="02460-176">Шаблон проекта Blazor Server: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="02460-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="02460-177">Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="02460-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="02460-178">Параметры шаблона можно просмотреть, передав параметр help (`-h` или `--help`) в команду CLI [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="02460-178">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
