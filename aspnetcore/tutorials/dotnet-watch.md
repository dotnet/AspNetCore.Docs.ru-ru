---
title: Разработка приложений ASP.NET Core с использованием наблюдателя файлов
author: rick-anderson
description: В этом руководстве показано, как установить и использовать наблюдатель за файлами .NET Core CLI (dotnet watch) в приложении ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 84cae3b3babe28c2ebf6dba50023b020112d1bb3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587584"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="2461e-103">Разработка приложений ASP.NET Core с использованием наблюдателя файлов</span><span class="sxs-lookup"><span data-stu-id="2461e-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="2461e-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Виктор Хурдугачи](https://twitter.com/victorhurdugaci) (Victor Hurdugaci)</span><span class="sxs-lookup"><span data-stu-id="2461e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="2461e-105">`dotnet watch` — это средство, которое запускает команду [.NET Core CLI](/dotnet/core/tools) при изменении исходных файлов.</span><span class="sxs-lookup"><span data-stu-id="2461e-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="2461e-106">Например, в результате изменения файла может выполняться компиляция, тестирование или развертывание.</span><span class="sxs-lookup"><span data-stu-id="2461e-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="2461e-107">В этом руководстве используется уже существующее приложение веб-API с двумя конечными точками, одна из которых возвращает сумму, а другая — произведение.</span><span class="sxs-lookup"><span data-stu-id="2461e-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="2461e-108">В методе произведения есть ошибка, которая исправлена в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="2461e-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="2461e-109">Скачайте [образец приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="2461e-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="2461e-110">Он содержит два проекта: *WebApp* (веб-API ASP.NET Core) и *WebAppTests* (модульные тесты для веб-API).</span><span class="sxs-lookup"><span data-stu-id="2461e-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="2461e-111">В командной оболочке перейдите в папку *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2461e-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="2461e-112">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2461e-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="2461e-113">Можно использовать `dotnet run --project <PROJECT>`, чтобы указать проект для запуска.</span><span class="sxs-lookup"><span data-stu-id="2461e-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="2461e-114">Например, при выполнении `dotnet run --project WebApp` из корневой папки примера приложения также будет запущен проект *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2461e-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="2461e-115">В выходных данных в консоли будут показаны аналогичные следующим сообщения, которые свидетельствуют о том, что приложение выполняется и ожидает запросов:</span><span class="sxs-lookup"><span data-stu-id="2461e-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="2461e-116">В браузере перейдите на адрес `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="2461e-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="2461e-117">Должен появиться результат `9`.</span><span class="sxs-lookup"><span data-stu-id="2461e-117">You should see the result of `9`.</span></span>

<span data-ttu-id="2461e-118">Перейдите к API продукта (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="2461e-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="2461e-119">Он возвращает `9`, а не `20`, как ожидалось.</span><span class="sxs-lookup"><span data-stu-id="2461e-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="2461e-120">Эта проблема устраняется далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="2461e-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="2461e-121">Добавление `dotnet watch` в проект</span><span class="sxs-lookup"><span data-stu-id="2461e-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="2461e-122">Средство наблюдения за файлами `dotnet watch` входит в версию 2.1.300 пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2461e-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="2461e-123">Выполните следующие действия при использовании более ранней версии пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2461e-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="2461e-124">Добавьте ссылку на пакет `Microsoft.DotNet.Watcher.Tools` в *CSPROJ*-файл:</span><span class="sxs-lookup"><span data-stu-id="2461e-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="2461e-125">Установите пакет `Microsoft.DotNet.Watcher.Tools`, запустив следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2461e-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="2461e-126">Запуск команд .NET Core CLI с помощью `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="2461e-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="2461e-127">Любую [команду .NET Core CLI](/dotnet/core/tools#cli-commands) можно запустить с `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="2461e-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="2461e-128">Пример:</span><span class="sxs-lookup"><span data-stu-id="2461e-128">For example:</span></span>

| <span data-ttu-id="2461e-129">Команда</span><span class="sxs-lookup"><span data-stu-id="2461e-129">Command</span></span> | <span data-ttu-id="2461e-130">Команда с контрольным значением</span><span class="sxs-lookup"><span data-stu-id="2461e-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="2461e-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="2461e-131">dotnet run</span></span> | <span data-ttu-id="2461e-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="2461e-132">dotnet watch run</span></span> |
| <span data-ttu-id="2461e-133">dotnet run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="2461e-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="2461e-134">dotnet watch run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="2461e-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="2461e-135">dotnet run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="2461e-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="2461e-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="2461e-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="2461e-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="2461e-137">dotnet test</span></span> | <span data-ttu-id="2461e-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="2461e-138">dotnet watch test</span></span> |

<span data-ttu-id="2461e-139">Запустите `dotnet watch run` в папке *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="2461e-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="2461e-140">В выходных данных консоли будет указано, что запущен `watch`.</span><span class="sxs-lookup"><span data-stu-id="2461e-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="2461e-141">При выполнении `dotnet watch run` в веб-приложении запускается браузер с переходом по URL-адресу приложения, когда оно будет готово.</span><span class="sxs-lookup"><span data-stu-id="2461e-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="2461e-142">Для этого `dotnet watch` считывает выходные данные из консоли приложения и ожидает, когда <xref:Microsoft.AspNetCore.WebHost> выведет сообщение о готовности.</span><span class="sxs-lookup"><span data-stu-id="2461e-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="2461e-143">`dotnet watch` обновляет содержимое в браузере при обнаружении изменений в отслеживаемых файлах.</span><span class="sxs-lookup"><span data-stu-id="2461e-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="2461e-144">Для этого команда watch вставляет в приложение ПО промежуточного слоя, которое изменяет ответы HTML, создаваемые приложением.</span><span class="sxs-lookup"><span data-stu-id="2461e-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="2461e-145">ПО промежуточного слоя добавляет на страницу блок скрипта JavaScript, позволяющий `dotnet watch` давать команду на обновление содержимого в браузере.</span><span class="sxs-lookup"><span data-stu-id="2461e-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="2461e-146">В настоящее время изменения в любых отслеживаемых файлах, включая статическое содержимое, такое как файлы *HTML* и *CSS*, приводят к повторной сборке приложения.</span><span class="sxs-lookup"><span data-stu-id="2461e-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="2461e-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="2461e-147">`dotnet watch`:</span></span>

* <span data-ttu-id="2461e-148">По умолчанию отслеживаются только файлы, которые влияют на сборку.</span><span class="sxs-lookup"><span data-stu-id="2461e-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="2461e-149">Дополнительные отслеживаемые файлы (указанные в конфигурации) по-прежнему приводят к сборке.</span><span class="sxs-lookup"><span data-stu-id="2461e-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="2461e-150">Дополнительные сведения о конфигурации см. в разделе [Конфигурация dotnet-watch](#dotnet-watch-configuration) этой документации.</span><span class="sxs-lookup"><span data-stu-id="2461e-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="2461e-151">Можно использовать `dotnet watch --project <PROJECT>`, чтобы указать проект для наблюдения.</span><span class="sxs-lookup"><span data-stu-id="2461e-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="2461e-152">Например, при выполнении `dotnet watch --project WebApp run` из корневой папки примера приложения также будет запущен проект *WebApp*, в том числе для наблюдения.</span><span class="sxs-lookup"><span data-stu-id="2461e-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="2461e-153">Изменения с помощью `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="2461e-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="2461e-154">Убедитесь, что `dotnet watch` выполняется.</span><span class="sxs-lookup"><span data-stu-id="2461e-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="2461e-155">Исправьте ошибку в методе `Product` для *MathController.cs* так, чтобы он возвращал произведение, а не сумму чисел.</span><span class="sxs-lookup"><span data-stu-id="2461e-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="2461e-156">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="2461e-156">Save the file.</span></span> <span data-ttu-id="2461e-157">В выходных данных консоли будет показано, что `dotnet watch` обнаружил изменение файла и перезапустил приложение.</span><span class="sxs-lookup"><span data-stu-id="2461e-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="2461e-158">Убедитесь, что `http://localhost:<port number>/api/math/product?a=4&b=5` возвращает правильный результат.</span><span class="sxs-lookup"><span data-stu-id="2461e-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="2461e-159">Тестирование с помощью `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="2461e-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="2461e-160">Снова измените метод `Product` для *MathController.cs* так, чтобы он возвращал сумму чисел.</span><span class="sxs-lookup"><span data-stu-id="2461e-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="2461e-161">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="2461e-161">Save the file.</span></span>
1. <span data-ttu-id="2461e-162">В командной оболочке перейдите в папку *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="2461e-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="2461e-163">Запустите [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="2461e-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="2461e-164">Запустите `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="2461e-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="2461e-165">В выходных данных будет указано, что проверка не пройдена и наблюдатель ожидает изменений в файле:</span><span class="sxs-lookup"><span data-stu-id="2461e-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="2461e-166">Исправьте код метода `Product` так, чтобы он возвращал произведение.</span><span class="sxs-lookup"><span data-stu-id="2461e-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="2461e-167">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="2461e-167">Save the file.</span></span>

<span data-ttu-id="2461e-168">`dotnet watch` обнаруживает изменения в файле и повторно запускает тесты.</span><span class="sxs-lookup"><span data-stu-id="2461e-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="2461e-169">В выходных данных консоли будет указано, что проверка пройдена.</span><span class="sxs-lookup"><span data-stu-id="2461e-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="2461e-170">Настройка списка файлов для наблюдения</span><span class="sxs-lookup"><span data-stu-id="2461e-170">Customize files list to watch</span></span>

<span data-ttu-id="2461e-171">По умолчанию `dotnet-watch` отслеживает все файлы, соответствующие следующим стандартным маскам:</span><span class="sxs-lookup"><span data-stu-id="2461e-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="2461e-172">Можно добавить в список наблюдения еще элементы, отредактировав файл *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2461e-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="2461e-173">Элементы можно указывать по отдельности или с помощью стандартной маски.</span><span class="sxs-lookup"><span data-stu-id="2461e-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="2461e-174">Удаление файлов из списка наблюдения</span><span class="sxs-lookup"><span data-stu-id="2461e-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="2461e-175">Можно настроить `dotnet-watch` так, чтобы игнорировать параметры по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2461e-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="2461e-176">Чтобы пропускать определенные файлы, добавьте атрибут `Watch="false"` в определение элемента в файле *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="2461e-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="2461e-177">Пользовательское наблюдение за проектами</span><span class="sxs-lookup"><span data-stu-id="2461e-177">Custom watch projects</span></span>

<span data-ttu-id="2461e-178">`dotnet-watch` используется не только с проектами C#.</span><span class="sxs-lookup"><span data-stu-id="2461e-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="2461e-179">Создайте пользовательское наблюдение за проектами для различных ситуаций.</span><span class="sxs-lookup"><span data-stu-id="2461e-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="2461e-180">Вы можете использовать следующий макет проекта:</span><span class="sxs-lookup"><span data-stu-id="2461e-180">Consider the following project layout:</span></span>

* <span data-ttu-id="2461e-181">**test/**</span><span class="sxs-lookup"><span data-stu-id="2461e-181">**test/**</span></span>
  * <span data-ttu-id="2461e-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="2461e-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="2461e-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="2461e-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="2461e-184">Если вы хотите наблюдать за обоими проектами, создайте пользовательский файл проекта, настроенный для наблюдения за обоими проектами:</span><span class="sxs-lookup"><span data-stu-id="2461e-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="2461e-185">Чтобы запустить наблюдение за файлами для обоих проектов, внесите изменения в папку *test*.</span><span class="sxs-lookup"><span data-stu-id="2461e-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="2461e-186">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="2461e-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="2461e-187">VSTest выполняется при изменении любого файла в любом из тестовых проектов.</span><span class="sxs-lookup"><span data-stu-id="2461e-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="2461e-188">Конфигурация dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="2461e-188">dotnet-watch configuration</span></span>

<span data-ttu-id="2461e-189">Некоторые параметры конфигурации можно передавать в `dotnet watch` с помощью переменных среды.</span><span class="sxs-lookup"><span data-stu-id="2461e-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="2461e-190">Доступные переменные:</span><span class="sxs-lookup"><span data-stu-id="2461e-190">The available variables are:</span></span>

| <span data-ttu-id="2461e-191">Параметр</span><span class="sxs-lookup"><span data-stu-id="2461e-191">Setting</span></span>  | <span data-ttu-id="2461e-192">Описание</span><span class="sxs-lookup"><span data-stu-id="2461e-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="2461e-193">Если задано значение 1 или true, `dotnet watch` использует опрашивающий наблюдатель за файлами вместо `FileSystemWatcher` в CoreFx.</span><span class="sxs-lookup"><span data-stu-id="2461e-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="2461e-194">Используется при отслеживании файлов в сетевых папках или подключенных томах Docker.</span><span class="sxs-lookup"><span data-stu-id="2461e-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="2461e-195">По умолчанию `dotnet watch` оптимизирует сборку, исключая определенные операции, такие как восстановление или повторная оценка набора отслеживаемых файлов при каждом изменении.</span><span class="sxs-lookup"><span data-stu-id="2461e-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="2461e-196">Если задано значение 1 или true, эти оптимизации отключены.</span><span class="sxs-lookup"><span data-stu-id="2461e-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="2461e-197">`dotnet watch run` пытается запускать веб-приложения в браузерах с использованием параметра `launchBrowser`, настроенного в файле *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2461e-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="2461e-198">Если задано значение 1 или true, это поведение подавляется.</span><span class="sxs-lookup"><span data-stu-id="2461e-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="2461e-199">`dotnet watch run` пытается обновлять содержимое в браузерах при обнаружении изменений в файлах.</span><span class="sxs-lookup"><span data-stu-id="2461e-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="2461e-200">Если задано значение 1 или true, это поведение подавляется.</span><span class="sxs-lookup"><span data-stu-id="2461e-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="2461e-201">Это поведение также подавляется, если задан параметр `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`.</span><span class="sxs-lookup"><span data-stu-id="2461e-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |
