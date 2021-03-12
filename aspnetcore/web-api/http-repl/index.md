---
title: Тестирование веб-API с помощью HttpRepl
author: scottaddie
description: Узнайте, как использовать глобальное средство .NET Core HttpRepl для просмотра и тестирования веб-API ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2020
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
uid: web-api/http-repl
ms.openlocfilehash: aeff3fd06719095660d1b3bb794ef74a8549f761
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585764"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="d56d2-103">Тестирование веб-API с помощью HttpRepl</span><span class="sxs-lookup"><span data-stu-id="d56d2-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="d56d2-104">Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="d56d2-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d56d2-105">HTTP read-eval-print loop (REPL):</span><span class="sxs-lookup"><span data-stu-id="d56d2-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="d56d2-106">это кроссплатформенная программа командной строки, которая поддерживается везде, где поддерживается .NET Core;</span><span class="sxs-lookup"><span data-stu-id="d56d2-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="d56d2-107">служит для создания HTTP-запросов с целью тестирования веб-API ASP.NET Core (а также веб-API, не связанных с ASP.NET Core) и просмотра их результатов;</span><span class="sxs-lookup"><span data-stu-id="d56d2-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="d56d2-108">может использоваться для тестирования веб-API, размещенных в любой среде, включая localhost и Службу приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="d56d2-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="d56d2-109">Поддерживаются следующие [HTTP-команды](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods):</span><span class="sxs-lookup"><span data-stu-id="d56d2-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="d56d2-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="d56d2-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="d56d2-111">GET</span><span class="sxs-lookup"><span data-stu-id="d56d2-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="d56d2-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="d56d2-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="d56d2-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="d56d2-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="d56d2-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="d56d2-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="d56d2-115">POST</span><span class="sxs-lookup"><span data-stu-id="d56d2-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="d56d2-116">PUT</span><span class="sxs-lookup"><span data-stu-id="d56d2-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="d56d2-117">Для выполнения дальнейших инструкций [просмотрите или скачайте пример веб-API ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/http-repl/samples) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d56d2-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d56d2-118">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="d56d2-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="d56d2-119">Установка</span><span class="sxs-lookup"><span data-stu-id="d56d2-119">Installation</span></span>

<span data-ttu-id="d56d2-120">Чтобы установить HttpRepl, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="d56d2-121">[Глобальное средство .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) устанавливается из пакета NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="d56d2-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="d56d2-122">Использование</span><span class="sxs-lookup"><span data-stu-id="d56d2-122">Usage</span></span>

<span data-ttu-id="d56d2-123">После успешной установки HttpRepl выполните следующую команду, чтобы запустить средство:</span><span class="sxs-lookup"><span data-stu-id="d56d2-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="d56d2-124">Чтобы просмотреть доступные команды HttpRepl, выполните одну из следующих команд:</span><span class="sxs-lookup"><span data-stu-id="d56d2-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="d56d2-125">Выводится следующий результат.</span><span class="sxs-lookup"><span data-stu-id="d56d2-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="d56d2-126">В HttpRepl поддерживается завершение команд.</span><span class="sxs-lookup"><span data-stu-id="d56d2-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="d56d2-127">Нажимая клавишу <kbd>TAB</kbd>, можно переходить по списку команд, которые начинаются с введенных символов или конечной точки API.</span><span class="sxs-lookup"><span data-stu-id="d56d2-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="d56d2-128">Доступные команды CLI описываются в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="d56d2-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="d56d2-129">Подключение к веб-API</span><span class="sxs-lookup"><span data-stu-id="d56d2-129">Connect to the web API</span></span>

<span data-ttu-id="d56d2-130">Чтобы подключиться к веб-API, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="d56d2-131">`<ROOT URI>` — это базовый универсальный код ресурса (URI) для веб-API.</span><span class="sxs-lookup"><span data-stu-id="d56d2-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="d56d2-132">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="d56d2-133">Кроме того, когда программа HttpRepl запущена, можно в любой момент выполнить следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="d56d2-134">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="d56d2-135">Создание ссылки на описание OpenAPI для веб-API вручную</span><span class="sxs-lookup"><span data-stu-id="d56d2-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="d56d2-136">Приведенная выше команда для подключения попытается автоматически найти описание OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d56d2-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="d56d2-137">Если по какой-то причине ей не удается это сделать, укажите для веб-API универсальный код ресурса (URI) для описания OpenAPI, используя параметр `--openapi`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="d56d2-138">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="d56d2-139">Включение подробного вывода для получения сведений о поиске, синтаксическом анализе и проверке описания OpenAPI</span><span class="sxs-lookup"><span data-stu-id="d56d2-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="d56d2-140">Если указать параметр `--verbose` с помощью команды `connect`, когда средство выполняет поиск описания OpenAPI, а также анализирует и проверяет его, будут получены дополнительные сведения.</span><span class="sxs-lookup"><span data-stu-id="d56d2-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="d56d2-141">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="d56d2-142">Просмотр веб-API</span><span class="sxs-lookup"><span data-stu-id="d56d2-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="d56d2-143">Просмотр доступных конечных точек</span><span class="sxs-lookup"><span data-stu-id="d56d2-143">View available endpoints</span></span>

<span data-ttu-id="d56d2-144">Чтобы получить список конечных точек (контроллеров) по текущему пути веб-API, выполните команду `ls` или `dir`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhost:5001/> ls
```

<span data-ttu-id="d56d2-145">Будут выведены данные в следующем формате:</span><span class="sxs-lookup"><span data-stu-id="d56d2-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="d56d2-146">Из приведенных выше результатов видно, что доступно два контроллера: `Fruits` и `People`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="d56d2-147">Они оба поддерживают операции HTTP GET и POST без параметров.</span><span class="sxs-lookup"><span data-stu-id="d56d2-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="d56d2-148">Более подробную информацию можно получить, перейдя к определенному контроллеру.</span><span class="sxs-lookup"><span data-stu-id="d56d2-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="d56d2-149">Например, из выходных данных приведенной ниже команды видно, что контроллер `Fruits` также поддерживает операции HTTP GET, PUT и DELETE.</span><span class="sxs-lookup"><span data-stu-id="d56d2-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="d56d2-150">Каждая из этих операций принимает параметр `id` в маршруте:</span><span class="sxs-lookup"><span data-stu-id="d56d2-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="d56d2-151">Кроме того, можно выполнить команду `ui`, чтобы открыть страницу с пользовательским интерфейсом Swagger веб-API в браузере.</span><span class="sxs-lookup"><span data-stu-id="d56d2-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="d56d2-152">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="d56d2-153">Переход к конечной точке</span><span class="sxs-lookup"><span data-stu-id="d56d2-153">Navigate to an endpoint</span></span>

<span data-ttu-id="d56d2-154">Чтобы перейти к другой конечной точке веб-API, выполните команду `cd`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="d56d2-155">В пути, указываемом после команды `cd`, регистр символов не учитывается.</span><span class="sxs-lookup"><span data-stu-id="d56d2-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="d56d2-156">Будут выведены данные в следующем формате:</span><span class="sxs-lookup"><span data-stu-id="d56d2-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="d56d2-157">Настройка HttpRepl</span><span class="sxs-lookup"><span data-stu-id="d56d2-157">Customize the HttpRepl</span></span>

<span data-ttu-id="d56d2-158">[Цвета](#set-color-preferences), используемые в HttpRepl по умолчанию, можно настроить.</span><span class="sxs-lookup"><span data-stu-id="d56d2-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="d56d2-159">Кроме того, можно определить [текстовый редактор по умолчанию](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="d56d2-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="d56d2-160">Настройки HttpRepl сохраняются на протяжении текущего сеанса и учитываются при открытии следующих сеансов.</span><span class="sxs-lookup"><span data-stu-id="d56d2-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="d56d2-161">Измененные настройки хранятся в следующем файле:</span><span class="sxs-lookup"><span data-stu-id="d56d2-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="d56d2-162">Linux</span><span class="sxs-lookup"><span data-stu-id="d56d2-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="d56d2-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d56d2-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="d56d2-164">macOS</span><span class="sxs-lookup"><span data-stu-id="d56d2-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="d56d2-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d56d2-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="d56d2-166">Windows</span><span class="sxs-lookup"><span data-stu-id="d56d2-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="d56d2-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d56d2-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="d56d2-168">Файл *HTTPREPLPREFS* загружается при запуске. Во время выполнения изменения в нем не отслеживаются.</span><span class="sxs-lookup"><span data-stu-id="d56d2-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="d56d2-169">Изменения, внесенные в него вручную, вступают в силу только после перезапуска средства.</span><span class="sxs-lookup"><span data-stu-id="d56d2-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="d56d2-170">Просмотр параметров</span><span class="sxs-lookup"><span data-stu-id="d56d2-170">View the settings</span></span>

<span data-ttu-id="d56d2-171">Чтобы просмотреть доступные параметры, выполните команду `pref get`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="d56d2-172">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="d56d2-173">В результате выполнения приведенной выше команды выводятся доступные пары "ключ-значение":</span><span class="sxs-lookup"><span data-stu-id="d56d2-173">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="d56d2-174">Настройки цветов</span><span class="sxs-lookup"><span data-stu-id="d56d2-174">Set color preferences</span></span>

<span data-ttu-id="d56d2-175">Раскрашивание ответов в настоящее время поддерживается только для JSON.</span><span class="sxs-lookup"><span data-stu-id="d56d2-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="d56d2-176">Чтобы настроить цвета по умолчанию в программе HttpRepl, найдите ключ, соответствующий изменяемому цвету.</span><span class="sxs-lookup"><span data-stu-id="d56d2-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="d56d2-177">Инструкции по поиску ключей см. в разделе [Просмотр параметров](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="d56d2-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="d56d2-178">Например, измените значение ключа `colors.json` с `Green` на `White` следующим образом:</span><span class="sxs-lookup"><span data-stu-id="d56d2-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="d56d2-179">Можно использовать только [допустимые цвета](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs).</span><span class="sxs-lookup"><span data-stu-id="d56d2-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="d56d2-180">При выполнении последующих HTTP-запросов к выходным данным будут применяться новые цвета.</span><span class="sxs-lookup"><span data-stu-id="d56d2-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="d56d2-181">Если определенные цветовые ключи не заданы, используются более общие ключи.</span><span class="sxs-lookup"><span data-stu-id="d56d2-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="d56d2-182">Рассмотрим это поведение на следующем примере:</span><span class="sxs-lookup"><span data-stu-id="d56d2-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="d56d2-183">Если у `colors.json.name` нет значения, используется `colors.json.string`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="d56d2-184">Если у `colors.json.string` нет значения, используется `colors.json.literal`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="d56d2-185">Если у `colors.json.literal` нет значения, используется `colors.json`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="d56d2-186">Если у `colors.json` нет значения, используется текст цвета по умолчанию для командной оболочки (`AllowedColors.None`).</span><span class="sxs-lookup"><span data-stu-id="d56d2-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="d56d2-187">Установка размера отступа</span><span class="sxs-lookup"><span data-stu-id="d56d2-187">Set indentation size</span></span>

<span data-ttu-id="d56d2-188">Настройка размера отступа в ответах в настоящее время поддерживается только для JSON.</span><span class="sxs-lookup"><span data-stu-id="d56d2-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="d56d2-189">Размер по умолчанию — два пробела.</span><span class="sxs-lookup"><span data-stu-id="d56d2-189">The default size is two spaces.</span></span> <span data-ttu-id="d56d2-190">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-190">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="d56d2-191">Чтобы изменить размер по умолчанию, задайте ключ `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="d56d2-192">Например, чтобы использовались четыре пробела, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="d56d2-193">В последующих ответах будет применяться отступ в четыре пробела:</span><span class="sxs-lookup"><span data-stu-id="d56d2-193">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="d56d2-194">Установка текстового редактора по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d56d2-194">Set the default text editor</span></span>

<span data-ttu-id="d56d2-195">По умолчанию текстовый редактор для HttpRepl не настроен.</span><span class="sxs-lookup"><span data-stu-id="d56d2-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="d56d2-196">Для тестирования методов веб-API, требующих текста HTTP-запроса, необходимо задать текстовый редактор по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d56d2-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="d56d2-197">Средство HttpRepl запускает настроенный текстовый редактор исключительно в целях составления текста запроса.</span><span class="sxs-lookup"><span data-stu-id="d56d2-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="d56d2-198">Чтобы указать текстовый редактор по умолчанию, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="d56d2-199">В приведенной выше команде `<EXECUTABLE>` — это полный путь к исполняемому файлу текстового редактора.</span><span class="sxs-lookup"><span data-stu-id="d56d2-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="d56d2-200">Например, чтобы задать Visual Studio Code как текстовый редактор по умолчанию, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="d56d2-201">Linux</span><span class="sxs-lookup"><span data-stu-id="d56d2-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="d56d2-202">macOS</span><span class="sxs-lookup"><span data-stu-id="d56d2-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="d56d2-203">Windows</span><span class="sxs-lookup"><span data-stu-id="d56d2-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="d56d2-204">Чтобы текстовый редактор по умолчанию запускался с определенными аргументами CLI, задайте ключ `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="d56d2-205">Например, предположим, что Visual Studio Code — это текстовый редактор по умолчанию и необходимо, чтобы средство HttpRepl всегда запускало сеанс Visual Studio Code с отключенными расширениями.</span><span class="sxs-lookup"><span data-stu-id="d56d2-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="d56d2-206">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="d56d2-207">Если Visual Studio Code является редактором по умолчанию, обычно вы хотите передать аргумент `-w` или `--wait`, чтобы Visual Studio Code принудительно ожидал, пока вы закроете файл перед возвратом.</span><span class="sxs-lookup"><span data-stu-id="d56d2-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="d56d2-208">Установка путей поиска описания OpenAPI</span><span class="sxs-lookup"><span data-stu-id="d56d2-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="d56d2-209">По умолчанию HttpRepl имеет набор относительных путей для поиска описания OpenAPI при выполнении команды `connect` без параметра `--openapi`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="d56d2-210">Эти относительные пути объединяются с корневыми и базовыми путями, указанными в команде `connect`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="d56d2-211">Относительные пути по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="d56d2-211">The default relative paths are:</span></span>

- <span data-ttu-id="d56d2-212">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="d56d2-212">*swagger.json*</span></span>
- <span data-ttu-id="d56d2-213">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="d56d2-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="d56d2-214">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="d56d2-214">*/swagger.json*</span></span>
- <span data-ttu-id="d56d2-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="d56d2-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="d56d2-216">*openapi.json*</span><span class="sxs-lookup"><span data-stu-id="d56d2-216">*openapi.json*</span></span>
- <span data-ttu-id="d56d2-217">*/openapi.json*</span><span class="sxs-lookup"><span data-stu-id="d56d2-217">*/openapi.json*</span></span>

<span data-ttu-id="d56d2-218">Чтобы использовать другой набор путей поиска в среде, используйте параметр `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="d56d2-219">Значение должно представлять собой список относительных путей, разделенных символом вертикальной черты.</span><span class="sxs-lookup"><span data-stu-id="d56d2-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="d56d2-220">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="d56d2-221">Вместо того чтобы полностью заменять список по умолчанию, его можно изменить, добавив или удалив пути.</span><span class="sxs-lookup"><span data-stu-id="d56d2-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="d56d2-222">Чтобы добавить один или несколько путей поиска в список по умолчанию, задайте параметр `swagger.addToSearchPaths`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="d56d2-223">Значение должно представлять собой список относительных путей, разделенных символом вертикальной черты.</span><span class="sxs-lookup"><span data-stu-id="d56d2-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="d56d2-224">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="d56d2-225">Чтобы удалить один или несколько путей поиска из списка по умолчанию, задайте параметр `swagger.addToSearchPaths`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="d56d2-226">Значение должно представлять собой список относительных путей, разделенных символом вертикальной черты.</span><span class="sxs-lookup"><span data-stu-id="d56d2-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="d56d2-227">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="d56d2-228">Тестирование HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="d56d2-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-229">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-230">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-231">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-232">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-232">Options</span></span>

<span data-ttu-id="d56d2-233">Для команды `get` доступны следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="d56d2-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d56d2-234">Пример</span><span class="sxs-lookup"><span data-stu-id="d56d2-234">Example</span></span>

<span data-ttu-id="d56d2-235">Чтобы отправить HTTP-запрос GET, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d56d2-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="d56d2-236">Выполните команду `get` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="d56d2-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="d56d2-237">Выходные данные приведенной выше команды имеют следующий формат:</span><span class="sxs-lookup"><span data-stu-id="d56d2-237">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="d56d2-238">Получите определенную запись, передав параметр в команду `get`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="d56d2-239">Выходные данные приведенной выше команды имеют следующий формат:</span><span class="sxs-lookup"><span data-stu-id="d56d2-239">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="d56d2-240">Тестирование HTTP-запросов POST</span><span class="sxs-lookup"><span data-stu-id="d56d2-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-241">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-242">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-243">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-244">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d56d2-245">Пример</span><span class="sxs-lookup"><span data-stu-id="d56d2-245">Example</span></span>

<span data-ttu-id="d56d2-246">Чтобы отправить HTTP-запрос POST, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d56d2-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="d56d2-247">Выполните команду `post` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="d56d2-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="d56d2-248">В приведенной выше команде в заголовке HTTP-запроса `Content-Type` указан формат текста запроса JSON.</span><span class="sxs-lookup"><span data-stu-id="d56d2-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d56d2-249">В текстовом редакторе по умолчанию открывается файл *TMP* с шаблоном JSON, представляющим текст HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d56d2-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d56d2-250">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d56d2-251">Инструкции по определению текстового редактора по умолчанию см. в разделе [Установка текстового редактора по умолчанию](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="d56d2-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d56d2-252">Измените шаблон JSON в соответствии с требованиями к проверке модели:</span><span class="sxs-lookup"><span data-stu-id="d56d2-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="d56d2-253">Сохраните файл *TMP* и закройте текстовый редактор.</span><span class="sxs-lookup"><span data-stu-id="d56d2-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d56d2-254">В командной оболочке появятся следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d56d2-254">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="d56d2-255">Тестирование HTTP-запросов PUT</span><span class="sxs-lookup"><span data-stu-id="d56d2-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-256">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-257">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-258">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-259">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d56d2-260">Пример</span><span class="sxs-lookup"><span data-stu-id="d56d2-260">Example</span></span>

<span data-ttu-id="d56d2-261">Чтобы отправить HTTP-запрос PUT, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d56d2-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="d56d2-262">*Необязательно:* Чтобы просмотреть данные перед их изменением, выполните команду `get`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="d56d2-263">Выполните команду `put` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="d56d2-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="d56d2-264">В приведенной выше команде в заголовке HTTP-запроса `Content-Type` указан формат текста запроса JSON.</span><span class="sxs-lookup"><span data-stu-id="d56d2-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d56d2-265">В текстовом редакторе по умолчанию открывается файл *TMP* с шаблоном JSON, представляющим текст HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d56d2-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d56d2-266">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d56d2-267">Инструкции по определению текстового редактора по умолчанию см. в разделе [Установка текстового редактора по умолчанию](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="d56d2-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d56d2-268">Измените шаблон JSON в соответствии с требованиями к проверке модели:</span><span class="sxs-lookup"><span data-stu-id="d56d2-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="d56d2-269">Сохраните файл *TMP* и закройте текстовый редактор.</span><span class="sxs-lookup"><span data-stu-id="d56d2-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d56d2-270">В командной оболочке появятся следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d56d2-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d56d2-271">*Необязательно:* Чтобы просмотреть изменения, выполните команду `get`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-271">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d56d2-272">Например, если вы ввели Cherry в текстовом редакторе, команда `get` вернет следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d56d2-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="d56d2-273">Тестирование HTTP-запросов DELETE</span><span class="sxs-lookup"><span data-stu-id="d56d2-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-274">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-275">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-276">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-277">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d56d2-278">Пример</span><span class="sxs-lookup"><span data-stu-id="d56d2-278">Example</span></span>

<span data-ttu-id="d56d2-279">Чтобы отправить HTTP-запрос DELETE, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="d56d2-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="d56d2-280">*Необязательно:* Чтобы просмотреть данные перед их изменением, выполните команду `get`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-280">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="d56d2-281">Выполните команду `delete` в конечной точке, которая поддерживает ее:</span><span class="sxs-lookup"><span data-stu-id="d56d2-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="d56d2-282">Выходные данные приведенной выше команды имеют следующий формат:</span><span class="sxs-lookup"><span data-stu-id="d56d2-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d56d2-283">*Необязательно:* Чтобы просмотреть изменения, выполните команду `get`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-283">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d56d2-284">В этом примере команда `get` возвращает следующие данные:</span><span class="sxs-lookup"><span data-stu-id="d56d2-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="d56d2-285">Тестирование HTTP-запросов PATCH</span><span class="sxs-lookup"><span data-stu-id="d56d2-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-286">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-287">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-288">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-289">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="d56d2-290">Тестирование HTTP-запросов HEAD</span><span class="sxs-lookup"><span data-stu-id="d56d2-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-291">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-292">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-293">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-294">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="d56d2-295">Тестирование HTTP-запросов OPTIONS</span><span class="sxs-lookup"><span data-stu-id="d56d2-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d56d2-296">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="d56d2-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d56d2-297">Аргументы</span><span class="sxs-lookup"><span data-stu-id="d56d2-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d56d2-298">Параметр маршрута, который требуется методу действия соответствующего контроллера.</span><span class="sxs-lookup"><span data-stu-id="d56d2-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d56d2-299">Параметры</span><span class="sxs-lookup"><span data-stu-id="d56d2-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="d56d2-300">Установка заголовков HTTP-запросов</span><span class="sxs-lookup"><span data-stu-id="d56d2-300">Set HTTP request headers</span></span>

<span data-ttu-id="d56d2-301">Задать заголовок HTTP-запроса можно одним из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="d56d2-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="d56d2-302">Внутри HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d56d2-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="d56d2-303">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="d56d2-304">При таком подходе для каждого заголовка HTTP-запроса требуется собственный параметр `-h`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="d56d2-305">Перед отправкой HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d56d2-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="d56d2-306">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="d56d2-307">Если заголовок задается перед отправкой запроса, он продолжает действовать на протяжении всего сеанса командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="d56d2-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="d56d2-308">Чтобы очистить заголовок, укажите пустое значение.</span><span class="sxs-lookup"><span data-stu-id="d56d2-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="d56d2-309">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="d56d2-310">Проверка защищенных конечных точек</span><span class="sxs-lookup"><span data-stu-id="d56d2-310">Test secured endpoints</span></span>

<span data-ttu-id="d56d2-311">HttpRepl поддерживает проверку защищенных конечных точек следующими способами:</span><span class="sxs-lookup"><span data-stu-id="d56d2-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="d56d2-312">С помощью учетных данных по умолчанию вошедшего в систему пользователя.</span><span class="sxs-lookup"><span data-stu-id="d56d2-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="d56d2-313">С помощью заголовков HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="d56d2-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="d56d2-314">Учетные данные по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d56d2-314">Default credentials</span></span>

<span data-ttu-id="d56d2-315">Рассмотрим тестируемый веб-API, который размещен в IIS и защищен с помощью проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="d56d2-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="d56d2-316">Необходимо, чтобы учетные данные пользователя, запускающего средство, передавались в тестируемые конечные точки HTTP.</span><span class="sxs-lookup"><span data-stu-id="d56d2-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="d56d2-317">Чтобы передать учетные данные по умолчанию вошедшего в систему пользователя, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="d56d2-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="d56d2-318">Установите для параметра `httpClient.useDefaultCredentials` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="d56d2-319">Закройте и перезапустите средство перед отправкой другого запроса в веб-API.</span><span class="sxs-lookup"><span data-stu-id="d56d2-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="d56d2-320">Учетные данные прокси-сервера по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d56d2-320">Default proxy credentials</span></span>

<span data-ttu-id="d56d2-321">Рассмотрим ситуацию, в которой тестируемый веб-API находится за прокси-сервером, защищенным с помощью проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="d56d2-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="d56d2-322">Необходимо, чтобы учетные данные пользователя, запустившего средство, передавались на прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="d56d2-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="d56d2-323">Чтобы передать учетные данные по умолчанию вошедшего в систему пользователя, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="d56d2-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="d56d2-324">Установите для параметра `httpClient.proxy.useDefaultCredentials` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="d56d2-325">Закройте и перезапустите средство перед отправкой другого запроса в веб-API.</span><span class="sxs-lookup"><span data-stu-id="d56d2-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="d56d2-326">Заголовки запросов HTTP</span><span class="sxs-lookup"><span data-stu-id="d56d2-326">HTTP request headers</span></span>

<span data-ttu-id="d56d2-327">Ниже приведены примеры поддерживаемых схем проверки подлинности и авторизации.</span><span class="sxs-lookup"><span data-stu-id="d56d2-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="d56d2-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="d56d2-328">basic authentication</span></span>
* <span data-ttu-id="d56d2-329">Токены носителя JWT</span><span class="sxs-lookup"><span data-stu-id="d56d2-329">JWT bearer tokens</span></span>
* <span data-ttu-id="d56d2-330">дайджест-аутентификация</span><span class="sxs-lookup"><span data-stu-id="d56d2-330">digest authentication</span></span>

<span data-ttu-id="d56d2-331">Например, можно отправить токен носителя в конечную точку с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="d56d2-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="d56d2-332">Для доступа к конечной точке, размещенной в Azure, или для использования [Azure REST API](/rest/api/azure/) необходим токен носителя.</span><span class="sxs-lookup"><span data-stu-id="d56d2-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="d56d2-333">Выполните следующие действия, чтобы получить токен носителя для подписки Azure с помощью [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="d56d2-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="d56d2-334">HttpRepl задает токен носителя в заголовке HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="d56d2-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="d56d2-335">Будет получен список веб-приложений службы приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="d56d2-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="d56d2-336">Войдите в Azure.</span><span class="sxs-lookup"><span data-stu-id="d56d2-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="d56d2-337">Получите идентификатор подписки с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="d56d2-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="d56d2-338">Скопируйте идентификатор подписки и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="d56d2-339">Получите токен носителя с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="d56d2-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="d56d2-340">Подключитесь к Azure REST API с помощью HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="d56d2-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="d56d2-341">Задайте заголовок запроса HTTP `Authorization`:</span><span class="sxs-lookup"><span data-stu-id="d56d2-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="d56d2-342">Перейдите к подписке:</span><span class="sxs-lookup"><span data-stu-id="d56d2-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="d56d2-343">Получите список веб-приложений Службы приложений Azure для вашей подписки:</span><span class="sxs-lookup"><span data-stu-id="d56d2-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="d56d2-344">Появится следующий ответ:</span><span class="sxs-lookup"><span data-stu-id="d56d2-344">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="d56d2-345">Включение и отключение отображения HTTP-запроса</span><span class="sxs-lookup"><span data-stu-id="d56d2-345">Toggle HTTP request display</span></span>

<span data-ttu-id="d56d2-346">По умолчанию отправляемый HTTP-запрос не отображается.</span><span class="sxs-lookup"><span data-stu-id="d56d2-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="d56d2-347">Соответствующую настройку можно изменить на всю длительность сеанса командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="d56d2-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="d56d2-348">Включение отображения запроса</span><span class="sxs-lookup"><span data-stu-id="d56d2-348">Enable request display</span></span>

<span data-ttu-id="d56d2-349">Чтобы отправляемый HTTP-запрос отображался, выполните команду `echo on`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="d56d2-350">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="d56d2-351">При отправке последующих HTTP-запросов в рамках текущего сеанса заголовки запросов будут отображаться.</span><span class="sxs-lookup"><span data-stu-id="d56d2-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="d56d2-352">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-352">For example:</span></span>

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="d56d2-353">Отключение отображения запроса</span><span class="sxs-lookup"><span data-stu-id="d56d2-353">Disable request display</span></span>

<span data-ttu-id="d56d2-354">Чтобы отправляемый HTTP-запрос не отображался, выполните команду `echo off`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="d56d2-355">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="d56d2-356">Выполнить сценарий</span><span class="sxs-lookup"><span data-stu-id="d56d2-356">Run a script</span></span>

<span data-ttu-id="d56d2-357">Если вы часто выполняете один и тот же набор команд HttpRepl, их можно сохранить в текстовом файле.</span><span class="sxs-lookup"><span data-stu-id="d56d2-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="d56d2-358">Команды в файле имеют тот же формат, что и выполняемые вручную в командной строке.</span><span class="sxs-lookup"><span data-stu-id="d56d2-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="d56d2-359">Их можно выполнять в пакетном режиме с помощью команды `run`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="d56d2-360">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-360">For example:</span></span>

1. <span data-ttu-id="d56d2-361">Создайте текстовый файл с набором команд, разделенных символами новой строки.</span><span class="sxs-lookup"><span data-stu-id="d56d2-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="d56d2-362">Например, это может быть файл *people-script.txt* со следующими командами:</span><span class="sxs-lookup"><span data-stu-id="d56d2-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="d56d2-363">Выполните команду `run`, передав в нее путь к текстовому файлу.</span><span class="sxs-lookup"><span data-stu-id="d56d2-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="d56d2-364">Пример:</span><span class="sxs-lookup"><span data-stu-id="d56d2-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="d56d2-365">Отображаются следующие результаты:</span><span class="sxs-lookup"><span data-stu-id="d56d2-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="d56d2-366">Очистка выходных данных</span><span class="sxs-lookup"><span data-stu-id="d56d2-366">Clear the output</span></span>

<span data-ttu-id="d56d2-367">Чтобы удалить все выходные данные средства HttpRepl из командной оболочки, выполните команду `clear` или `cls`.</span><span class="sxs-lookup"><span data-stu-id="d56d2-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="d56d2-368">Например, предположим, что в командной оболочке есть следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d56d2-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="d56d2-369">Чтобы очистить их, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d56d2-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="d56d2-370">После выполнения приведенной выше команды командная оболочка содержит только следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d56d2-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="d56d2-371">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d56d2-371">Additional resources</span></span>

* [<span data-ttu-id="d56d2-372">Запросы к REST API</span><span class="sxs-lookup"><span data-stu-id="d56d2-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="d56d2-373">Репозиторий HttpRepl в GitHub</span><span class="sxs-lookup"><span data-stu-id="d56d2-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
* [<span data-ttu-id="d56d2-374">Настройка Visual Studio для запуска HttpRepl</span><span class="sxs-lookup"><span data-stu-id="d56d2-374">Configure Visual Studio to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [<span data-ttu-id="d56d2-375">Настройка Visual Studio Code для запуска HttpRepl</span><span class="sxs-lookup"><span data-stu-id="d56d2-375">Configure Visual Studio Code to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [<span data-ttu-id="d56d2-376">Настройка Visual Studio для Mac для запуска HttpRepl</span><span class="sxs-lookup"><span data-stu-id="d56d2-376">Configure Visual Studio for Mac to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
