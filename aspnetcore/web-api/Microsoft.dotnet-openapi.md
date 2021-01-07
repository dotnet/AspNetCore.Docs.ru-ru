---
title: Разработка приложений ASP.NET Core с использованием OpenAPI
author: ryanbrandenburg
description: Здесь демонстрируется, как добавить ссылки в файлы OpenAPI с использованием средства Microsoft.dotnet-openapi.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972032"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="efe92-103">Разработка приложений ASP.NET Core с использованием средств OpenAPI</span><span class="sxs-lookup"><span data-stu-id="efe92-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="efe92-104">Автор: Райан Бранденбург (Ryan Brandenburg)</span><span class="sxs-lookup"><span data-stu-id="efe92-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="efe92-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) — это [глобальное средство .NET Core](/dotnet/core/tools/global-tools) для управления ссылками [OpenAPI](https://github.com/OAI/OpenAPI-Specification) в рамках проекта.</span><span class="sxs-lookup"><span data-stu-id="efe92-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="efe92-106">Установка</span><span class="sxs-lookup"><span data-stu-id="efe92-106">Installation</span></span>

<span data-ttu-id="efe92-107">Чтобы установить `Microsoft.dotnet-openapi`, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="efe92-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="efe92-108">Добавить</span><span class="sxs-lookup"><span data-stu-id="efe92-108">Add</span></span>

<span data-ttu-id="efe92-109">Добавление ссылки OpenAPI с помощью любой команды на этой странице добавляет `<OpenApiReference />` в *CSPROJ* -файл элемент, аналогичный следующему:</span><span class="sxs-lookup"><span data-stu-id="efe92-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="efe92-110">Для вызова созданного клиентского кода приложению требуется предыдущая ссылка.</span><span class="sxs-lookup"><span data-stu-id="efe92-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="efe92-111">Добавление файла</span><span class="sxs-lookup"><span data-stu-id="efe92-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="efe92-112">Параметры</span><span class="sxs-lookup"><span data-stu-id="efe92-112">Options</span></span>

| <span data-ttu-id="efe92-113">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-113">Short option</span></span>| <span data-ttu-id="efe92-114">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-114">Long option</span></span>| <span data-ttu-id="efe92-115">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-115">Description</span></span> | <span data-ttu-id="efe92-116">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="efe92-117">-p</span><span class="sxs-lookup"><span data-stu-id="efe92-117">-p</span></span>|<span data-ttu-id="efe92-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="efe92-118">--updateProject</span></span> | <span data-ttu-id="efe92-119">Проект для выполнения операции.</span><span class="sxs-lookup"><span data-stu-id="efe92-119">The project to operate on.</span></span> |<span data-ttu-id="efe92-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="efe92-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="efe92-121">-c</span><span class="sxs-lookup"><span data-stu-id="efe92-121">-c</span></span>|<span data-ttu-id="efe92-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="efe92-122">--code-generator</span></span>| <span data-ttu-id="efe92-123">Генератор кода, применяемый к ссылке.</span><span class="sxs-lookup"><span data-stu-id="efe92-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="efe92-124">Возможные значения: `NSwagCSharp` и `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="efe92-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="efe92-125">Если атрибут `--code-generator` не задан, по умолчанию для средств будет выбрано `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="efe92-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="efe92-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="efe92-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="efe92-127">-H</span><span class="sxs-lookup"><span data-stu-id="efe92-127">-h</span></span>|<span data-ttu-id="efe92-128">--help</span><span class="sxs-lookup"><span data-stu-id="efe92-128">--help</span></span>|<span data-ttu-id="efe92-129">Отображение справочных сведений.</span><span class="sxs-lookup"><span data-stu-id="efe92-129">Show help information</span></span>|<span data-ttu-id="efe92-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="efe92-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="efe92-131">Аргументы</span><span class="sxs-lookup"><span data-stu-id="efe92-131">Arguments</span></span>

|  <span data-ttu-id="efe92-132">Аргумент</span><span class="sxs-lookup"><span data-stu-id="efe92-132">Argument</span></span>  | <span data-ttu-id="efe92-133">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-133">Description</span></span> | <span data-ttu-id="efe92-134">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="efe92-135">source-file</span><span class="sxs-lookup"><span data-stu-id="efe92-135">source-file</span></span> | <span data-ttu-id="efe92-136">Источник, из которого создается ссылка.</span><span class="sxs-lookup"><span data-stu-id="efe92-136">The source to create a reference from.</span></span> <span data-ttu-id="efe92-137">Должен быть файлом OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="efe92-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="efe92-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="efe92-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="efe92-139">Добавление URL-адреса</span><span class="sxs-lookup"><span data-stu-id="efe92-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="efe92-140">Параметры</span><span class="sxs-lookup"><span data-stu-id="efe92-140">Options</span></span>

| <span data-ttu-id="efe92-141">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-141">Short option</span></span>| <span data-ttu-id="efe92-142">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-142">Long option</span></span>| <span data-ttu-id="efe92-143">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-143">Description</span></span> | <span data-ttu-id="efe92-144">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="efe92-145">-p</span><span class="sxs-lookup"><span data-stu-id="efe92-145">-p</span></span>|<span data-ttu-id="efe92-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="efe92-146">--updateProject</span></span> | <span data-ttu-id="efe92-147">Проект для выполнения операции.</span><span class="sxs-lookup"><span data-stu-id="efe92-147">The project to operate on.</span></span> |<span data-ttu-id="efe92-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="efe92-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="efe92-149">-o</span><span class="sxs-lookup"><span data-stu-id="efe92-149">-o</span></span>|<span data-ttu-id="efe92-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="efe92-150">--output-file</span></span> | <span data-ttu-id="efe92-151">Место размещения локальной копии файла OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="efe92-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="efe92-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="efe92-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="efe92-153">-c</span><span class="sxs-lookup"><span data-stu-id="efe92-153">-c</span></span>|<span data-ttu-id="efe92-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="efe92-154">--code-generator</span></span>| <span data-ttu-id="efe92-155">Генератор кода, применяемый к ссылке.</span><span class="sxs-lookup"><span data-stu-id="efe92-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="efe92-156">Возможные значения: `NSwagCSharp` и `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="efe92-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="efe92-157">Добавление URL-адреса DotNet openapi `https://contoso.com/openapi.json` — генератор кода</span><span class="sxs-lookup"><span data-stu-id="efe92-157">dotnet openapi add url `https://contoso.com/openapi.json` --code-generator</span></span>
| <span data-ttu-id="efe92-158">-H</span><span class="sxs-lookup"><span data-stu-id="efe92-158">-h</span></span>|<span data-ttu-id="efe92-159">--help</span><span class="sxs-lookup"><span data-stu-id="efe92-159">--help</span></span>|<span data-ttu-id="efe92-160">Отображение справочных сведений.</span><span class="sxs-lookup"><span data-stu-id="efe92-160">Show help information</span></span>|<span data-ttu-id="efe92-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="efe92-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="efe92-162">Аргументы</span><span class="sxs-lookup"><span data-stu-id="efe92-162">Arguments</span></span>

|  <span data-ttu-id="efe92-163">Аргумент</span><span class="sxs-lookup"><span data-stu-id="efe92-163">Argument</span></span>  | <span data-ttu-id="efe92-164">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-164">Description</span></span> | <span data-ttu-id="efe92-165">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="efe92-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="efe92-166">source-URL</span></span> | <span data-ttu-id="efe92-167">Источник, из которого создается ссылка.</span><span class="sxs-lookup"><span data-stu-id="efe92-167">The source to create a reference from.</span></span> <span data-ttu-id="efe92-168">Должен быть URL-адресом.</span><span class="sxs-lookup"><span data-stu-id="efe92-168">Must be a URL.</span></span> |<span data-ttu-id="efe92-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="efe92-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="efe92-170">Удалить</span><span class="sxs-lookup"><span data-stu-id="efe92-170">Remove</span></span>

<span data-ttu-id="efe92-171">Удаляет ссылку на OpenAPI, соответствующую заданному имени файла, из файла *CSPROJ*.</span><span class="sxs-lookup"><span data-stu-id="efe92-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="efe92-172">При удалении ссылки OpenAPI клиенты не будут создаваться.</span><span class="sxs-lookup"><span data-stu-id="efe92-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="efe92-173">Локальные файлы *JSON* и *YAML* удаляются.</span><span class="sxs-lookup"><span data-stu-id="efe92-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="efe92-174">Параметры</span><span class="sxs-lookup"><span data-stu-id="efe92-174">Options</span></span>

| <span data-ttu-id="efe92-175">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-175">Short option</span></span>| <span data-ttu-id="efe92-176">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-176">Long option</span></span>| <span data-ttu-id="efe92-177">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-177">Description</span></span>| <span data-ttu-id="efe92-178">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="efe92-179">-p</span><span class="sxs-lookup"><span data-stu-id="efe92-179">-p</span></span>|<span data-ttu-id="efe92-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="efe92-180">--updateProject</span></span> | <span data-ttu-id="efe92-181">Проект для выполнения операции.</span><span class="sxs-lookup"><span data-stu-id="efe92-181">The project to operate on.</span></span> |<span data-ttu-id="efe92-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="efe92-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="efe92-183">-H</span><span class="sxs-lookup"><span data-stu-id="efe92-183">-h</span></span>|<span data-ttu-id="efe92-184">--help</span><span class="sxs-lookup"><span data-stu-id="efe92-184">--help</span></span>|<span data-ttu-id="efe92-185">Отображение справочных сведений.</span><span class="sxs-lookup"><span data-stu-id="efe92-185">Show help information</span></span>|<span data-ttu-id="efe92-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="efe92-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="efe92-187">Аргументы</span><span class="sxs-lookup"><span data-stu-id="efe92-187">Arguments</span></span>

|  <span data-ttu-id="efe92-188">Аргумент</span><span class="sxs-lookup"><span data-stu-id="efe92-188">Argument</span></span>  | <span data-ttu-id="efe92-189">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-189">Description</span></span>| <span data-ttu-id="efe92-190">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="efe92-191">source-file</span><span class="sxs-lookup"><span data-stu-id="efe92-191">source-file</span></span> | <span data-ttu-id="efe92-192">Источник, ссылку на который необходимо удалить.</span><span class="sxs-lookup"><span data-stu-id="efe92-192">The source to remove the reference to.</span></span> |<span data-ttu-id="efe92-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="efe92-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="efe92-194">Обновить</span><span class="sxs-lookup"><span data-stu-id="efe92-194">Refresh</span></span>

<span data-ttu-id="efe92-195">Обновляет локальную версию файла, скачанного с использованием последнего содержимого из URL-адреса для скачивания.</span><span class="sxs-lookup"><span data-stu-id="efe92-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="efe92-196">Параметры</span><span class="sxs-lookup"><span data-stu-id="efe92-196">Options</span></span>

| <span data-ttu-id="efe92-197">Короткий параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-197">Short option</span></span>| <span data-ttu-id="efe92-198">Длинный параметр</span><span class="sxs-lookup"><span data-stu-id="efe92-198">Long option</span></span>| <span data-ttu-id="efe92-199">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-199">Description</span></span> | <span data-ttu-id="efe92-200">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="efe92-201">-p</span><span class="sxs-lookup"><span data-stu-id="efe92-201">-p</span></span>|<span data-ttu-id="efe92-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="efe92-202">--updateProject</span></span> | <span data-ttu-id="efe92-203">Проект для выполнения операции.</span><span class="sxs-lookup"><span data-stu-id="efe92-203">The project to operate on.</span></span> | <span data-ttu-id="efe92-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="efe92-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="efe92-205">-H</span><span class="sxs-lookup"><span data-stu-id="efe92-205">-h</span></span>|<span data-ttu-id="efe92-206">--help</span><span class="sxs-lookup"><span data-stu-id="efe92-206">--help</span></span>|<span data-ttu-id="efe92-207">Отображение справочных сведений.</span><span class="sxs-lookup"><span data-stu-id="efe92-207">Show help information</span></span>|<span data-ttu-id="efe92-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="efe92-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="efe92-209">Аргументы</span><span class="sxs-lookup"><span data-stu-id="efe92-209">Arguments</span></span>

|  <span data-ttu-id="efe92-210">Аргумент</span><span class="sxs-lookup"><span data-stu-id="efe92-210">Argument</span></span>  | <span data-ttu-id="efe92-211">Описание</span><span class="sxs-lookup"><span data-stu-id="efe92-211">Description</span></span> | <span data-ttu-id="efe92-212">Пример</span><span class="sxs-lookup"><span data-stu-id="efe92-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="efe92-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="efe92-213">source-URL</span></span> | <span data-ttu-id="efe92-214">URL-адрес, ссылку из которого необходимо обновить.</span><span class="sxs-lookup"><span data-stu-id="efe92-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="efe92-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="efe92-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
