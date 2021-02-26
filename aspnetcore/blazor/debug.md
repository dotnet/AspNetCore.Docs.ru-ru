---
title: Отладка в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 9214fa10a2bf7d53a4cb12263a3fa69bded84b29
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536237"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7a223-103">Отладка в ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7a223-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7a223-104">Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Microsoft Edge или Chrome).</span><span class="sxs-lookup"><span data-stu-id="7a223-104">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="7a223-105">Отлаживать приложение можно также с помощью следующих интегрированных сред разработки (IDE).</span><span class="sxs-lookup"><span data-stu-id="7a223-105">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="7a223-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a223-106">Visual Studio</span></span>
* <span data-ttu-id="7a223-107">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="7a223-107">Visual Studio for Mac</span></span>
* <span data-ttu-id="7a223-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a223-108">Visual Studio Code</span></span>

<span data-ttu-id="7a223-109">Поддерживаются следующие сценарии:</span><span class="sxs-lookup"><span data-stu-id="7a223-109">Available scenarios include:</span></span>

* <span data-ttu-id="7a223-110">Установка и удаление точек останова.</span><span class="sxs-lookup"><span data-stu-id="7a223-110">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7a223-111">Запуск приложения с поддержкой отладки в IDE.</span><span class="sxs-lookup"><span data-stu-id="7a223-111">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="7a223-112">Пошаговое прохождение кода.</span><span class="sxs-lookup"><span data-stu-id="7a223-112">Single-step through the code.</span></span>
* <span data-ttu-id="7a223-113">Возобновление выполнения кода с помощью сочетания клавиш в IDE.</span><span class="sxs-lookup"><span data-stu-id="7a223-113">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="7a223-114">Просмотр значений локальных переменных в окне *Локальные переменные*.</span><span class="sxs-lookup"><span data-stu-id="7a223-114">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="7a223-115">Просмотр стека вызовов, включая цепочки вызовов между JavaScript и .NET.</span><span class="sxs-lookup"><span data-stu-id="7a223-115">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="7a223-116">Сейчас вам *недоступны* следующие функции:</span><span class="sxs-lookup"><span data-stu-id="7a223-116">For now, you *can't*:</span></span>

* <span data-ttu-id="7a223-117">Останов при необработанных исключениях.</span><span class="sxs-lookup"><span data-stu-id="7a223-117">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7a223-118">Попадание в точки останова во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-118">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7a223-119">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-119">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="7a223-120">Отладка в нелокальных сценариях (например, [в подсистеме Windows для Linux (WSL)](/windows/wsl/) или [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span><span class="sxs-lookup"><span data-stu-id="7a223-120">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>
* <span data-ttu-id="7a223-121">Автоматическое перестроение серверного приложения `*Server*` для размещенного решения Blazor во время отладки, например посредством запуска приложения с [`dotnet watch run`](xref:tutorials/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="7a223-121">Automatically rebuild the backend `*Server*` app of a hosted Blazor solution during debugging, for example by running the app with [`dotnet watch run`](xref:tutorials/dotnet-watch).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a223-122">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="7a223-122">Prerequisites</span></span>

<span data-ttu-id="7a223-123">Для отладки требуется один из следующих браузеров:</span><span class="sxs-lookup"><span data-stu-id="7a223-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7a223-124">Google Chrome (версии 70 и более поздних) (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="7a223-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="7a223-125">Microsoft Edge (версии 80 и более поздних)</span><span class="sxs-lookup"><span data-stu-id="7a223-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="7a223-126">Убедитесь, что брандмауэры или прокси-серверы не блокируют обмен данными с прокси-сервером отладки (процесс `NodeJS`).</span><span class="sxs-lookup"><span data-stu-id="7a223-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="7a223-127">Дополнительные сведения см. в разделе [Настройка брандмауэра](#firewall-configuration).</span><span class="sxs-lookup"><span data-stu-id="7a223-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="7a223-128">Для Visual Studio для Mac требуется версия 8.8 (сборка 1532) или более поздняя.</span><span class="sxs-lookup"><span data-stu-id="7a223-128">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="7a223-129">Установите последнюю версию Visual Studio для Mac, нажав кнопку **Скачать Visual Studio для Mac** на странице [Microsoft: Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="7a223-129">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="7a223-130">Выберите в Visual Studio канал *Предварительная версия*.</span><span class="sxs-lookup"><span data-stu-id="7a223-130">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="7a223-131">Дополнительные сведения см. в статье [Установка предварительной версии Visual Studio для Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="7a223-131">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="7a223-132">В настоящее время Apple Safari в macOS не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7a223-132">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="7a223-133">Включение отладки</span><span class="sxs-lookup"><span data-stu-id="7a223-133">Enable debugging</span></span>

<span data-ttu-id="7a223-134">Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл `launchSettings.json` в запускаемом проекте, добавив в него следующее свойство `inspectUri` в каждом профиле запуска:</span><span class="sxs-lookup"><span data-stu-id="7a223-134">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7a223-135">После изменения файл `launchSettings.json` должен выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="7a223-135">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7a223-136">Свойство `inspectUri`:</span><span class="sxs-lookup"><span data-stu-id="7a223-136">The `inspectUri` property:</span></span>

* <span data-ttu-id="7a223-137">Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="7a223-137">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7a223-138">Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.</span><span class="sxs-lookup"><span data-stu-id="7a223-138">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="7a223-139">Значения заполнителей для протокола WebSocket (`wsProtocol`), узла (`url.hostname`), порта (`url.port`) и URI инспектора в запущенном браузере (`browserInspectUri`) предоставляются платформой.</span><span class="sxs-lookup"><span data-stu-id="7a223-139">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7a223-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a223-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a223-141">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7a223-141">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7a223-142">Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a223-142">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7a223-143">Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="7a223-143">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7a223-144">**Запуск без отладки** (<kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7a223-144">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="7a223-145">При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="7a223-145">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="7a223-146">В `*Client*` приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="7a223-146">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="7a223-147">В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me**, чтобы попасть в точку останова.</span><span class="sxs-lookup"><span data-stu-id="7a223-147">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="7a223-148">В Visual Studio просмотрите значение поля `currentCount` в окне **Локальные переменные**.</span><span class="sxs-lookup"><span data-stu-id="7a223-148">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="7a223-149">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="7a223-149">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7a223-150">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код.</span><span class="sxs-lookup"><span data-stu-id="7a223-150">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="7a223-151">Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="7a223-151">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7a223-152">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="7a223-152">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7a223-153">Перейдите на страницу `Fetch Data`, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер.</span><span class="sxs-lookup"><span data-stu-id="7a223-153">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="7a223-154">Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите в точку останова на сервере в `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="7a223-154">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="7a223-155">Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды в браузере.</span><span class="sxs-lookup"><span data-stu-id="7a223-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="7a223-156">Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-156">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7a223-157">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-157">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="7a223-158">Если приложение находится по [базовому пути](xref:blazor/host-and-deploy/index#app-base-path), отличному от `/`, измените следующие свойства в `Properties/launchSettings.json` в соответствии с базовым путем приложения:</span><span class="sxs-lookup"><span data-stu-id="7a223-158">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="7a223-159">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="7a223-159">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="7a223-160">`inspectUri` каждого профиля:</span><span class="sxs-lookup"><span data-stu-id="7a223-160">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="7a223-161">Заполнители в указанных выше параметрах:</span><span class="sxs-lookup"><span data-stu-id="7a223-161">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="7a223-162">`{INSECURE PORT}` — незащищенный порт;</span><span class="sxs-lookup"><span data-stu-id="7a223-162">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="7a223-163">по умолчанию предоставляется случайное значение, но допускается пользовательский порт;</span><span class="sxs-lookup"><span data-stu-id="7a223-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="7a223-164">`{APP BASE PATH}` — базовый путь приложения;</span><span class="sxs-lookup"><span data-stu-id="7a223-164">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="7a223-165">`{SECURE PORT}` — защищенный порт;</span><span class="sxs-lookup"><span data-stu-id="7a223-165">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="7a223-166">по умолчанию предоставляется случайное значение, но допускается пользовательский порт;</span><span class="sxs-lookup"><span data-stu-id="7a223-166">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="7a223-167">`{PROFILE 1, 2, ... N}` — профили параметров запуска.</span><span class="sxs-lookup"><span data-stu-id="7a223-167">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="7a223-168">Как правило, в приложении по умолчанию указывается более одного профиля (например, профиль для IIS Express и профиль проекта, который используется сервером Kestrel).</span><span class="sxs-lookup"><span data-stu-id="7a223-168">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="7a223-169">В следующих примерах приложение размещается в `/OAT` с базовым путем, настроенным в `wwwroot/index.html` как `<base href="/OAT/">`:</span><span class="sxs-lookup"><span data-stu-id="7a223-169">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="7a223-170">Сведения об использовании пользовательского базового пути для приложений Blazor WebAssembly см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="7a223-170">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7a223-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7a223-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="7a223-172">Отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7a223-172">Debug standalone Blazor WebAssembly</span></span></h2>

<span data-ttu-id="7a223-173">Сведения о настройке ресурсов VS Code в папке `.vscode` см. в руководстве по операционной системе **Linux** в <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="7a223-173">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="7a223-174">Откройте автономное приложение Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="7a223-174">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="7a223-175">Вы можете получить уведомление о том, что для включения отладки требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="7a223-175">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="7a223-176">Для отладки приложений Blazor WebAssembly требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="7a223-176">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="7a223-177">Если вы получили уведомление:</span><span class="sxs-lookup"><span data-stu-id="7a223-177">If you receive the notification:</span></span>

   * <span data-ttu-id="7a223-178">Убедитесь, что установлена актуальная версия [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="7a223-178">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="7a223-179">Чтобы проверить установленные расширения, откройте раздел **Вид** > **Расширения** в строке меню или выберите значок **Расширения** на боковой панели **Действия**.</span><span class="sxs-lookup"><span data-stu-id="7a223-179">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="7a223-180">Убедитесь, что отладчик JavaScript включен.</span><span class="sxs-lookup"><span data-stu-id="7a223-180">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="7a223-181">Откройте файл параметров в строке меню (**Файл** > **Настройки** > **Параметры**).</span><span class="sxs-lookup"><span data-stu-id="7a223-181">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="7a223-182">Выполните поиск по ключевым словам `debug preview`.</span><span class="sxs-lookup"><span data-stu-id="7a223-182">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="7a223-183">В результатах поиска убедитесь, что флажок **Отладка > JavaScript: Use Preview (JavaScript: использовать предварительную версию)** установлен.</span><span class="sxs-lookup"><span data-stu-id="7a223-183">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="7a223-184">Если параметр включения отладки предварительной версии отсутствует, обновите VS Code до актуальной версии или установите [расширение отладчика JavaScript](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code версии 1.46 или более ранней).</span><span class="sxs-lookup"><span data-stu-id="7a223-184">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="7a223-185">Перезагрузите окно.</span><span class="sxs-lookup"><span data-stu-id="7a223-185">Reload the window.</span></span>

1. <span data-ttu-id="7a223-186">Начните отладку, нажав клавишу <kbd>F5</kbd> или выбрав соответствующий пункт меню.</span><span class="sxs-lookup"><span data-stu-id="7a223-186">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7a223-187">**Запуск без отладки** (<kbd>CTRL</kbd>+<kbd>F5</kbd>) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7a223-187">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="7a223-188">При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="7a223-188">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="7a223-189">При появлении запроса выберите вариант **Blazor WebAssembly Debug** (Отладка Blazor WebAssembly), чтобы начать отладку.</span><span class="sxs-lookup"><span data-stu-id="7a223-189">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="7a223-190">Автономное приложение запустится и откроется браузер отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-190">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="7a223-191">В `*Client*` приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="7a223-191">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="7a223-192">В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me**, чтобы попасть в точку останова.</span><span class="sxs-lookup"><span data-stu-id="7a223-192">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="7a223-193">Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-193">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7a223-194">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-194">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="7a223-195">Отладка размещенного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7a223-195">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="7a223-196">Откройте папку решения размещенного приложения Blazor WebAssembly в VS Code.</span><span class="sxs-lookup"><span data-stu-id="7a223-196">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="7a223-197">Если для проекта не задана конфигурация запуска, появится следующее уведомление.</span><span class="sxs-lookup"><span data-stu-id="7a223-197">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="7a223-198">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="7a223-198">Select **Yes**.</span></span>

   > <span data-ttu-id="7a223-199">В {ИМЯ ПРИЛОЖЕНИЯ} отсутствуют необходимые ресурсы для сборки и отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-199">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="7a223-200">Добавить их?</span><span class="sxs-lookup"><span data-stu-id="7a223-200">Add them?</span></span>

   <span data-ttu-id="7a223-201">Сведения о настройке ресурсов VS Code в папке `.vscode` см. в руководстве по операционной системе **Linux** в <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="7a223-201">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="7a223-202">В палитре команд вверху окна выберите проект *Server* в размещенном решении.</span><span class="sxs-lookup"><span data-stu-id="7a223-202">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="7a223-203">При этом будет создан файл `launch.json` с конфигурацией запуска для отладчика.</span><span class="sxs-lookup"><span data-stu-id="7a223-203">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7a223-204">Подключение к существующему сеансу отладки</span><span class="sxs-lookup"><span data-stu-id="7a223-204">Attach to an existing debugging session</span></span>

<span data-ttu-id="7a223-205">Чтобы подключиться к выполняющемуся приложению Blazor, создайте файл `launch.json` со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="7a223-205">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7a223-206">Подключение к сеансу отладки поддерживается только для автономных приложений.</span><span class="sxs-lookup"><span data-stu-id="7a223-206">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="7a223-207">Чтобы использовать отладку полного стека, необходимо запустить приложение из VS Code.</span><span class="sxs-lookup"><span data-stu-id="7a223-207">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="7a223-208">Параметры конфигурации запуска</span><span class="sxs-lookup"><span data-stu-id="7a223-208">Launch configuration options</span></span>

<span data-ttu-id="7a223-209">Для отладки типа `blazorwasm` (`.vscode/launch.json`) поддерживаются следующие параметры конфигурации запуска.</span><span class="sxs-lookup"><span data-stu-id="7a223-209">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="7a223-210">Параметр</span><span class="sxs-lookup"><span data-stu-id="7a223-210">Option</span></span>    | <span data-ttu-id="7a223-211">Описание</span><span class="sxs-lookup"><span data-stu-id="7a223-211">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7a223-212">Используйте `launch`, чтобы запустить и подключить сеанс отладки к приложению Blazor WebAssembly, или `attach`, чтобы подключить сеанс отладки к уже работающему приложению.</span><span class="sxs-lookup"><span data-stu-id="7a223-212">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="7a223-213">URL-адрес для открытия в браузере при отладке.</span><span class="sxs-lookup"><span data-stu-id="7a223-213">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7a223-214">По умолчанию — `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="7a223-214">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="7a223-215">Браузер для запуска сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-215">The browser to launch for the debugging session.</span></span> <span data-ttu-id="7a223-216">Задайте `edge` или `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7a223-216">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7a223-217">По умолчанию — `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7a223-217">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="7a223-218">Используется для создания журналов из отладчика JS.</span><span class="sxs-lookup"><span data-stu-id="7a223-218">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7a223-219">Задано значение `true`, чтобы создавались журналы.</span><span class="sxs-lookup"><span data-stu-id="7a223-219">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="7a223-220">При запуске и отладке размещенного приложения Blazor WebAssembly необходимо задать значение `true`.</span><span class="sxs-lookup"><span data-stu-id="7a223-220">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="7a223-221">Указывает полный путь к веб-серверу.</span><span class="sxs-lookup"><span data-stu-id="7a223-221">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="7a223-222">Если приложение обслуживается с использованием подмаршрута, необходимо указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="7a223-222">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="7a223-223">Количество миллисекунд ожидания до подключения сеанса отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-223">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="7a223-224">По умолчанию — 30 000 миллисекунд (30 секунд).</span><span class="sxs-lookup"><span data-stu-id="7a223-224">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="7a223-225">Ссылка на исполняемый файл для запуска сервера размещенного приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-225">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="7a223-226">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="7a223-226">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="7a223-227">Рабочий каталог для запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-227">The working directory to launch the app under.</span></span> <span data-ttu-id="7a223-228">Если для `hosted` задано значение `true`, нужно указать значение для этого параметра.</span><span class="sxs-lookup"><span data-stu-id="7a223-228">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="7a223-229">Переменные среды, которые должны быть предоставлены запущенному процессу.</span><span class="sxs-lookup"><span data-stu-id="7a223-229">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="7a223-230">Применимо, только если для параметра `hosted` задано значение `true`.</span><span class="sxs-lookup"><span data-stu-id="7a223-230">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="7a223-231">Примеры конфигураций запуска</span><span class="sxs-lookup"><span data-stu-id="7a223-231">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="7a223-232">Запуск и отладка автономного приложения Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7a223-232">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7a223-233">Подключение к выполняющемуся приложению по указанному URL-адресу</span><span class="sxs-lookup"><span data-stu-id="7a223-233">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-blazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="7a223-234">Запуск и отладка размещенного приложения Blazor WebAssembly с помощью Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="7a223-234">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="7a223-235">По умолчанию конфигурация браузера использует Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="7a223-235">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="7a223-236">При использовании Microsoft Edge для отладки задайте для параметра `browser` значение `edge`.</span><span class="sxs-lookup"><span data-stu-id="7a223-236">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="7a223-237">Чтобы использовать Google Chrome, не задавайте параметр `browser` или задайте для него значение `chrome`.</span><span class="sxs-lookup"><span data-stu-id="7a223-237">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="7a223-238">В предыдущем примере `MyHostedApp.Server.dll` является сборкой приложения *Server*.</span><span class="sxs-lookup"><span data-stu-id="7a223-238">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="7a223-239">Папка `.vscode` расположена в папке решения рядом с папками `Client`, `Server` и `Shared`.</span><span class="sxs-lookup"><span data-stu-id="7a223-239">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7a223-240">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="7a223-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7a223-241">Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio для Mac, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="7a223-241">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="7a223-242">Создайте новое приложение Blazor WebAssembly, размещенное в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a223-242">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7a223-243">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы запустить приложение в отладчике.</span><span class="sxs-lookup"><span data-stu-id="7a223-243">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="7a223-244">**Запуск без отладки** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="7a223-244">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="7a223-245">При запуске приложения в конфигурации отладки затраты на отладку всегда приводят к небольшому снижению производительности.</span><span class="sxs-lookup"><span data-stu-id="7a223-245">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="7a223-246">Для сеанса отладки должен быть выбран браузер Google Chrome или Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="7a223-246">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="7a223-247">В `*Client*` приложении установите точку останова в строке `currentCount++;` в `Pages/Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="7a223-247">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="7a223-248">В браузере перейдите на страницу `Counter` и нажмите кнопку **Click me**, чтобы попасть в точку останова.</span><span class="sxs-lookup"><span data-stu-id="7a223-248">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="7a223-249">В Visual Studio просмотрите значение поля `currentCount` в окне **Локальные переменные**.</span><span class="sxs-lookup"><span data-stu-id="7a223-249">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="7a223-250">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы продолжить выполнение.</span><span class="sxs-lookup"><span data-stu-id="7a223-250">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="7a223-251">При отладке приложения Blazor WebAssembly можно также отлаживать серверный код.</span><span class="sxs-lookup"><span data-stu-id="7a223-251">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="7a223-252">Установите точку останова на странице `Pages/FetchData.razor` в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="7a223-252">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7a223-253">Установите точку останова в `WeatherForecastController` в методе действия `Get`.</span><span class="sxs-lookup"><span data-stu-id="7a223-253">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7a223-254">Перейдите на страницу `Fetch Data`, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер.</span><span class="sxs-lookup"><span data-stu-id="7a223-254">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="7a223-255">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`.</span><span class="sxs-lookup"><span data-stu-id="7a223-255">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="7a223-256">Нажмите <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды в браузере.</span><span class="sxs-lookup"><span data-stu-id="7a223-256">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="7a223-257">Попадание в точки останова **не** происходит во время запуска приложения перед запуском прокси-сервера отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-257">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="7a223-258">Сюда входят точки останова в `Program.Main` (`Program.cs`) и точки останова в [методах `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) компонентов, загружаемых первой страницей, запрошенной из приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-258">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="7a223-259">Дополнительные сведения см. в статье [Отладка с помощью Visual Studio для Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="7a223-259">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="7a223-260">Отладка в браузере</span><span class="sxs-lookup"><span data-stu-id="7a223-260">Debug in the browser</span></span>

<span data-ttu-id="7a223-261">*Инструкции в этом разделе относятся к Google Chrome и Microsoft Edge в Windows.*</span><span class="sxs-lookup"><span data-stu-id="7a223-261">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="7a223-262">Запустите отладочную сборку приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="7a223-262">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7a223-263">Запустите браузер и перейдите по URL-адресу приложения (например, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="7a223-263">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="7a223-264">В браузере попытайтесь запустить удаленную отладку, нажав клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="7a223-264">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="7a223-265">Браузер должен работать с включенной удаленной отладкой (это не поведение по умолчанию).</span><span class="sxs-lookup"><span data-stu-id="7a223-265">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="7a223-266">Если удаленная отладка отключена, отобразится страница с ошибкой **Не удается найти вкладку браузера для отладки** с инструкциями по запуску браузера с открытым портом отладки.</span><span class="sxs-lookup"><span data-stu-id="7a223-266">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="7a223-267">Выполните инструкции в браузере, после чего откроется новое окно браузера.</span><span class="sxs-lookup"><span data-stu-id="7a223-267">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="7a223-268">Закройте предыдущее окно.</span><span class="sxs-lookup"><span data-stu-id="7a223-268">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="7a223-269">Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш из предыдущего шага.</span><span class="sxs-lookup"><span data-stu-id="7a223-269">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="7a223-270">Через некоторое время на вкладке **Источники** отобразится список сборок .NET приложения в узле `file://`.</span><span class="sxs-lookup"><span data-stu-id="7a223-270">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="7a223-271">Точки остановка, заданные в коде компонента (файлы `.razor`) и в файлах с кодом C# (`.cs`), будут срабатывать при выполнении кода.</span><span class="sxs-lookup"><span data-stu-id="7a223-271">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="7a223-272">После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="7a223-272">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="7a223-273">Blazor предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET.</span><span class="sxs-lookup"><span data-stu-id="7a223-273">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7a223-274">При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="7a223-274">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7a223-275">Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).</span><span class="sxs-lookup"><span data-stu-id="7a223-275">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7a223-276">Сопоставители с исходным кодом в браузере</span><span class="sxs-lookup"><span data-stu-id="7a223-276">Browser source maps</span></span>

<span data-ttu-id="7a223-277">Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="7a223-277">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7a223-278">Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="7a223-278">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7a223-279">Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.</span><span class="sxs-lookup"><span data-stu-id="7a223-279">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="7a223-280">Настройка брандмауэра</span><span class="sxs-lookup"><span data-stu-id="7a223-280">Firewall configuration</span></span>

<span data-ttu-id="7a223-281">Если брандмауэр блокирует обмен данными с прокси-сервером отладки, создайте правило исключения брандмауэра, которое разрешает обмен данными между браузером и процессом `NodeJS`.</span><span class="sxs-lookup"><span data-stu-id="7a223-281">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="7a223-282">Чтобы избежать создания уязвимостей в области безопасности, необходимо внести изменения в конфигурацию брандмауэра.</span><span class="sxs-lookup"><span data-stu-id="7a223-282">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="7a223-283">Старательно применяйте рекомендации по безопасности, следуйте рекомендациям по обеспечению безопасности и учитывайте предупреждения, выдаваемые производителем брандмауэра.</span><span class="sxs-lookup"><span data-stu-id="7a223-283">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="7a223-284">Разрешение начала обмена данными с `NodeJS` процессом:</span><span class="sxs-lookup"><span data-stu-id="7a223-284">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="7a223-285">Открывает сервер узла для любого подключения в зависимости от возможностей и конфигурации брандмауэра.</span><span class="sxs-lookup"><span data-stu-id="7a223-285">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="7a223-286">Может быть рискованным в зависимости от вашей сети.</span><span class="sxs-lookup"><span data-stu-id="7a223-286">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="7a223-287">**Рекомендуется использовать только на компьютерах разработчиков.**</span><span class="sxs-lookup"><span data-stu-id="7a223-287">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="7a223-288">Если возможно, разрешайте открытый обмен данными с `NodeJS` процессом **только в доверенных или частных сетях**.</span><span class="sxs-lookup"><span data-stu-id="7a223-288">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="7a223-289">Инструкции по настройке [брандмауэра Windows](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) см. в статье [Создание правила для входящего трафика для программы или службы](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="7a223-289">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="7a223-290">Дополнительные сведения см. в статье [Брандмауэр Защитника Windows с расширенными функциями безопасности](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) и в связанных статьях в наборе документации по брандмауэру Windows.</span><span class="sxs-lookup"><span data-stu-id="7a223-290">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7a223-291">Диагностика</span><span class="sxs-lookup"><span data-stu-id="7a223-291">Troubleshoot</span></span>

<span data-ttu-id="7a223-292">При возникновении ошибок воспользуйтесь следующими советами:</span><span class="sxs-lookup"><span data-stu-id="7a223-292">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="7a223-293">На вкладке **Отладчик** откройте средства разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="7a223-293">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7a223-294">В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.</span><span class="sxs-lookup"><span data-stu-id="7a223-294">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="7a223-295">Убедитесь, что вы установили и сделали доверенным сертификат разработки HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7a223-295">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="7a223-296">Для получения дополнительной информации см. <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="7a223-296">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="7a223-297">В Visual Studio должен быть включен параметр **Включить отладку JavaScript для ASP.NET (Chrome, Microsoft Edge и IE)** в разделе **Инструменты** > **Параметры** > **Отладка** > **Общие**.</span><span class="sxs-lookup"><span data-stu-id="7a223-297">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="7a223-298">Этот параметр включен в Visual Studio по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7a223-298">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="7a223-299">Если отладка не работает, проверьте, выбран ли этот параметр.</span><span class="sxs-lookup"><span data-stu-id="7a223-299">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="7a223-300">Если в среде используется прокси-сервер HTTP, необходимо включить `localhost` в параметры обхода прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="7a223-300">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="7a223-301">Для этого можно задать переменную среды `NO_PROXY` в одном из двух мест:</span><span class="sxs-lookup"><span data-stu-id="7a223-301">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="7a223-302">файл `launchSettings.json` проекта;</span><span class="sxs-lookup"><span data-stu-id="7a223-302">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="7a223-303">на уровне пользовательских или системных переменных среды для применения ко всем приложениям.</span><span class="sxs-lookup"><span data-stu-id="7a223-303">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="7a223-304">Перезапустите Visual Studio, чтобы изменение переменной среды вступило в силу.</span><span class="sxs-lookup"><span data-stu-id="7a223-304">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="7a223-305">Убедитесь, что брандмауэры или прокси-серверы не блокируют обмен данными с прокси-сервером отладки (процесс `NodeJS`).</span><span class="sxs-lookup"><span data-stu-id="7a223-305">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="7a223-306">Дополнительные сведения см. в разделе [Настройка брандмауэра](#firewall-configuration).</span><span class="sxs-lookup"><span data-stu-id="7a223-306">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="7a223-307">Точки останова в `OnInitialized{Async}` не срабатывают</span><span class="sxs-lookup"><span data-stu-id="7a223-307">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="7a223-308">Прокси-сервер платформы Blazor для отладки запускается не мгновенно, поэтому точки останова в[методе жизненного цикла `OnInitialized{Async}`](xref:blazor/components/lifecycle#component-initialization-methods) могут не срабатывать.</span><span class="sxs-lookup"><span data-stu-id="7a223-308">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="7a223-309">Мы рекомендуем добавить задержку в начале тела метода, чтобы прокси-сервер отладки успел запуститься до срабатывания точки останова.</span><span class="sxs-lookup"><span data-stu-id="7a223-309">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="7a223-310">Вы можете включить задержку на основе [директивы компилятора `if`](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), чтобы обеспечить отсутствие задержки для конечной сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="7a223-310">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="7a223-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="7a223-311"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="7a223-312"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="7a223-312"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="7a223-313">истекло время ожидания Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="7a223-313">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="7a223-314">Если Visual Studio вызывает исключение о том, что не удалось запустить адаптер отладки, указав, что истекло время ожидания, то можно настроить время ожидания с помощью параметра реестра.</span><span class="sxs-lookup"><span data-stu-id="7a223-314">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="7a223-315">Заполнитель `{TIMEOUT}` в предыдущей команде указывается в миллисекундах.</span><span class="sxs-lookup"><span data-stu-id="7a223-315">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="7a223-316">Например, одной минуте соответствует значение `60000`.</span><span class="sxs-lookup"><span data-stu-id="7a223-316">For example, one minute is assigned as `60000`.</span></span>
