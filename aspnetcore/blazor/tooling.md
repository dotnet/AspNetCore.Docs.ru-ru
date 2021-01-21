---
title: Инструменты для Blazor ASP.NET Core
author: guardrex
description: Сведения об инструментах, доступных для создания приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
ms.openlocfilehash: 5901a1cb693dfe8e34e62ce2a28456bcf584221c
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252270"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="b16dd-103">Инструменты для Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b16dd-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="b16dd-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b16dd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="b16dd-105">Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="b16dd-106">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="b16dd-106">Create a new project.</span></span>

1. <span data-ttu-id="b16dd-107">Выберите **Приложение Blazor** .</span><span class="sxs-lookup"><span data-stu-id="b16dd-107">Select **Blazor App**.</span></span> <span data-ttu-id="b16dd-108">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-108">Select **Next**.</span></span>

1. <span data-ttu-id="b16dd-109">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b16dd-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b16dd-110">Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое.</span><span class="sxs-lookup"><span data-stu-id="b16dd-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="b16dd-111">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-111">Select **Create**.</span></span>

1. <span data-ttu-id="b16dd-112">Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b16dd-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b16dd-113">Для работы с Blazor Server выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="b16dd-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b16dd-114">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-114">Select **Create**.</span></span>

   <span data-ttu-id="b16dd-115">Для работы с размещенной платформой Blazor WebAssembly установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="b16dd-116">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b16dd-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b16dd-117">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="b16dd-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="b16dd-118">Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="b16dd-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="b16dd-119">Установите последнюю версию [пакета SDK для .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="b16dd-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="b16dd-120">Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b16dd-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="b16dd-121">Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="b16dd-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="b16dd-122">Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="b16dd-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="b16dd-123">Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b16dd-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="b16dd-124">Для работы с размещенной платформой Blazor WebAssembly добавьте параметр hosted (`-ho` или `--hosted`) в команду.</span><span class="sxs-lookup"><span data-stu-id="b16dd-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="b16dd-125">Для работы с Blazor Server выполните следующую команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="b16dd-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="b16dd-126">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b16dd-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b16dd-127">Откройте папку `WebApplication1` в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b16dd-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="b16dd-128">Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта.</span><span class="sxs-lookup"><span data-stu-id="b16dd-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="b16dd-129">Выберите **Да**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-129">Select **Yes**.</span></span>

1. <span data-ttu-id="b16dd-130">Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="b16dd-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="b16dd-131">Установка доверия к сертификату разработки</span><span class="sxs-lookup"><span data-stu-id="b16dd-131">Trust a development certificate</span></span>

<span data-ttu-id="b16dd-132">В Linux отсутствует централизованный способ установки доверия к сертификату.</span><span class="sxs-lookup"><span data-stu-id="b16dd-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="b16dd-133">Обычно используется один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="b16dd-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="b16dd-134">исключение URL-адреса приложения в списке исключений браузера;</span><span class="sxs-lookup"><span data-stu-id="b16dd-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="b16dd-135">установка доверия ко всем самозаверяющим сертификатам для `localhost`;</span><span class="sxs-lookup"><span data-stu-id="b16dd-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="b16dd-136">добавление сертификата в список доверенных сертификатов в браузере.</span><span class="sxs-lookup"><span data-stu-id="b16dd-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="b16dd-137">Дополнительные сведения см. в рекомендациях, предоставляемых для вашего браузера и дистрибутива Linux.</span><span class="sxs-lookup"><span data-stu-id="b16dd-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="b16dd-138">Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="b16dd-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="b16dd-139">Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="b16dd-140">На боковой панели выберите **Интернет и консоль** > **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="b16dd-141">Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** .</span><span class="sxs-lookup"><span data-stu-id="b16dd-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="b16dd-142">Для работы с Blazor Server выберите шаблон **приложения Blazor Server** .</span><span class="sxs-lookup"><span data-stu-id="b16dd-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="b16dd-143">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-143">Select **Next**.</span></span>

   <span data-ttu-id="b16dd-144">Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* (автономной и размещенной) и *Blazor Server* , см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b16dd-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="b16dd-145">Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="b16dd-146">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-146">Select **Next**.</span></span>

1. <span data-ttu-id="b16dd-147">Для работы с размещенной платформой Blazor WebAssembly установите флажок **Размещенный проект ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="b16dd-148">В поле **Имя проекта** присвойте имя приложению `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="b16dd-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="b16dd-149">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b16dd-149">Select **Create**.</span></span>

1. <span data-ttu-id="b16dd-150">Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*.</span><span class="sxs-lookup"><span data-stu-id="b16dd-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="b16dd-151">Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.</span><span class="sxs-lookup"><span data-stu-id="b16dd-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="b16dd-152">При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.</span><span class="sxs-lookup"><span data-stu-id="b16dd-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="b16dd-153">Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.</span><span class="sxs-lookup"><span data-stu-id="b16dd-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="b16dd-154">Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="b16dd-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="b16dd-155">Использование Visual Studio для кроссплатформенной разработки на Blazor</span><span class="sxs-lookup"><span data-stu-id="b16dd-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="b16dd-156">[Visual Studio Code](https://code.visualstudio.com/) — это кроссплатформенная интегрированная среда разработки (IDE) с открытым кодом, которую можно использовать для разработки приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="b16dd-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="b16dd-157">Используйте .NET CLI для создания приложения Blazor, которое будет разрабатываться в Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="b16dd-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="b16dd-158">Дополнительные сведения см. в [варианте этой статьи для Linux](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="b16dd-158">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="b16dd-159">Параметры шаблона Blazor</span><span class="sxs-lookup"><span data-stu-id="b16dd-159">Blazor template options</span></span>

<span data-ttu-id="b16dd-160">Платформа Blazor предоставляет шаблоны для создания приложений с использованием каждой из двух моделей размещения Blazor.</span><span class="sxs-lookup"><span data-stu-id="b16dd-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="b16dd-161">Шаблоны служат для создания проектов и решений Blazor независимо от того, какие средства выбраны для разработки на Blazor (Visual Studio, Visual Studio для Mac, Visual Studio Code или .NET CLI).</span><span class="sxs-lookup"><span data-stu-id="b16dd-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="b16dd-162">Шаблон проекта Blazor WebAssembly: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="b16dd-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="b16dd-163">Шаблон проекта Blazor Server: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="b16dd-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="b16dd-164">Дополнительные сведения о моделях размещения Blazor см. в статье <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="b16dd-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="b16dd-165">Параметры шаблона можно просмотреть, передав параметр help (`-h` или `--help`) в команду CLI [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="b16dd-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```