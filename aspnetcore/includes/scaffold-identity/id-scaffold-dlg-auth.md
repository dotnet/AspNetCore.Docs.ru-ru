---
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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551282"
---
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aaad3-101">Запустите программу Identity формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="aaad3-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaad3-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaad3-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaad3-103">В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить** > **Новый** шаблонный элемент.</span><span class="sxs-lookup"><span data-stu-id="aaad3-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aaad3-104">В левой области диалогового окна **Добавление шаблона** выберите **Identity** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="aaad3-105">В диалоговом окне **Добавить Identity** выберите нужные параметры.</span><span class="sxs-lookup"><span data-stu-id="aaad3-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="aaad3-106">Выберите существующую страницу макета, чтобы файл макета не перезаписывался неверной разметкой.</span><span class="sxs-lookup"><span data-stu-id="aaad3-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="aaad3-107">При выборе существующего файла *\_ Layout. cshtml* он **не** перезаписывается.</span><span class="sxs-lookup"><span data-stu-id="aaad3-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="aaad3-108">Пример:</span><span class="sxs-lookup"><span data-stu-id="aaad3-108">For example:</span></span>
    * <span data-ttu-id="aaad3-109">`~/Pages/Shared/_Layout.cshtml` для Razor страниц или Blazor Server проектов с Razor инфраструктурой существующих страниц</span><span class="sxs-lookup"><span data-stu-id="aaad3-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="aaad3-110">`~/Views/Shared/_Layout.cshtml` для проектов или Blazor Server проектов MVC с существующей инфраструктурой MVC</span><span class="sxs-lookup"><span data-stu-id="aaad3-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="aaad3-111">Чтобы использовать существующий контекст данных, выберите по меньшей мере один файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="aaad3-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="aaad3-112">Необходимо выбрать по крайней мере один файл для добавления контекста данных.</span><span class="sxs-lookup"><span data-stu-id="aaad3-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="aaad3-113">Выберите класс контекста данных.</span><span class="sxs-lookup"><span data-stu-id="aaad3-113">Select your data context class.</span></span>
  * <span data-ttu-id="aaad3-114">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-114">Select **Add**.</span></span>
* <span data-ttu-id="aaad3-115">Чтобы создать новый контекст пользователя и, возможно, создать настраиваемый класс пользователя для Identity :</span><span class="sxs-lookup"><span data-stu-id="aaad3-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="aaad3-116">Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="aaad3-117">Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="aaad3-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="aaad3-118">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-118">Select **Add**.</span></span>

<span data-ttu-id="aaad3-119">Примечание. Если вы создаете новый контекст пользователя, вам не нужно выбирать файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="aaad3-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aaad3-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="aaad3-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aaad3-121">Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:</span><span class="sxs-lookup"><span data-stu-id="aaad3-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="aaad3-122">Добавьте необходимые ссылки на пакет NuGet в файл проекта (*CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="aaad3-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="aaad3-123">Выполните следующие команды в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="aaad3-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="aaad3-124">Выполните следующую команду, чтобы вывести список Identity параметров шаблона:</span><span class="sxs-lookup"><span data-stu-id="aaad3-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="aaad3-125">В папке проекта запустите Identity механизм формирования шаблонов с нужными параметрами.</span><span class="sxs-lookup"><span data-stu-id="aaad3-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="aaad3-126">Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="aaad3-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="aaad3-127">Используйте правильное полное имя для контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="aaad3-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="aaad3-128">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="aaad3-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="aaad3-129">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="aaad3-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="aaad3-130">Пример:</span><span class="sxs-lookup"><span data-stu-id="aaad3-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="aaad3-131">При запуске Identity механизма формирования шаблонов без указания `--files` флага или `--useDefaultUI` флага все доступные Identity страницы пользовательского интерфейса будут созданы в проекте.</span><span class="sxs-lookup"><span data-stu-id="aaad3-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aaad3-132">Запустите программу Identity формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="aaad3-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaad3-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaad3-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaad3-134">В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить** > **Новый** шаблонный элемент.</span><span class="sxs-lookup"><span data-stu-id="aaad3-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="aaad3-135">В левой области диалогового окна **Добавление шаблона** выберите **Identity** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="aaad3-136">В диалоговом окне **Добавить Identity** выберите нужные параметры.</span><span class="sxs-lookup"><span data-stu-id="aaad3-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="aaad3-137">Выберите существующую страницу макета, или файл макета будет перезаписан с неверной разметкой.</span><span class="sxs-lookup"><span data-stu-id="aaad3-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="aaad3-138">При выборе существующего файла *\_ Layout. cshtml* он **не** перезаписывается.</span><span class="sxs-lookup"><span data-stu-id="aaad3-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="aaad3-139">Пример:</span><span class="sxs-lookup"><span data-stu-id="aaad3-139">For example:</span></span>
    * <span data-ttu-id="aaad3-140">`~/Pages/Shared/_Layout.cshtml` для Razor страниц</span><span class="sxs-lookup"><span data-stu-id="aaad3-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="aaad3-141">`~/Views/Shared/_Layout.cshtml` для проектов MVC</span><span class="sxs-lookup"><span data-stu-id="aaad3-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="aaad3-142">Чтобы использовать существующий контекст данных, выберите по меньшей мере один файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="aaad3-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="aaad3-143">Необходимо выбрать по крайней мере один файл для добавления контекста данных.</span><span class="sxs-lookup"><span data-stu-id="aaad3-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="aaad3-144">Выберите класс контекста данных.</span><span class="sxs-lookup"><span data-stu-id="aaad3-144">Select your data context class.</span></span>
  * <span data-ttu-id="aaad3-145">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-145">Select **Add**.</span></span>
* <span data-ttu-id="aaad3-146">Чтобы создать новый контекст пользователя и, возможно, создать настраиваемый класс пользователя для Identity :</span><span class="sxs-lookup"><span data-stu-id="aaad3-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="aaad3-147">Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="aaad3-148">Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="aaad3-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="aaad3-149">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="aaad3-149">Select **Add**.</span></span>

<span data-ttu-id="aaad3-150">Примечание. Если вы создаете новый контекст пользователя, вам не нужно выбирать файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="aaad3-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aaad3-151">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="aaad3-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aaad3-152">Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:</span><span class="sxs-lookup"><span data-stu-id="aaad3-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="aaad3-153">Добавьте ссылку на пакет в [Microsoft. VisualStudio. Web. стратегию. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) в файл проекта (*CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="aaad3-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="aaad3-154">Выполните следующие команды в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="aaad3-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="aaad3-155">Выполните следующую команду, чтобы вывести список Identity параметров шаблона:</span><span class="sxs-lookup"><span data-stu-id="aaad3-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="aaad3-156">В папке проекта запустите Identity механизм формирования шаблонов с нужными параметрами.</span><span class="sxs-lookup"><span data-stu-id="aaad3-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="aaad3-157">Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="aaad3-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="aaad3-158">Используйте правильное полное имя для контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="aaad3-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="aaad3-159">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="aaad3-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="aaad3-160">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="aaad3-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="aaad3-161">Пример:</span><span class="sxs-lookup"><span data-stu-id="aaad3-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="aaad3-162">При запуске Identity механизма формирования шаблонов без указания `--files` флага или `--useDefaultUI` флага все доступные Identity страницы пользовательского интерфейса будут созданы в проекте.</span><span class="sxs-lookup"><span data-stu-id="aaad3-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
