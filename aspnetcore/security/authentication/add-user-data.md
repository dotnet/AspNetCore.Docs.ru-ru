---
title: Добавление, скачивание и удаление данных пользователя Identity в проекте ASP.NET Core
author: rick-anderson
description: Узнайте, как добавлять пользовательские данные Identity в проект ASP.NET Core. Удаление данных на GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: 2d921a0c72fb7c03cd88966077e2d33e4b19ffa1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585920"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="06f4c-104">Добавление, скачивание и удаление пользовательских данных Identity в проекте ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06f4c-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="06f4c-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="06f4c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="06f4c-106">В этой статье показано, как сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="06f4c-106">This article shows how to:</span></span>

* <span data-ttu-id="06f4c-107">Добавление настраиваемых пользовательских данных в веб-приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="06f4c-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="06f4c-108">Пометьте пользовательскую модель данных <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> атрибутом, чтобы она автоматически была доступна для скачивания и удаления.</span><span class="sxs-lookup"><span data-stu-id="06f4c-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="06f4c-109">Обеспечение возможности загрузки и удаления данных помогает удовлетворить требования [GDPR](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="06f4c-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="06f4c-110">Пример проекта создается на основе Razor веб-приложения для страниц, но эти инструкции похожи на ASP.NET Core веб-приложение MVC.</span><span class="sxs-lookup"><span data-stu-id="06f4c-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="06f4c-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/add-user-data) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06f4c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06f4c-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="06f4c-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="06f4c-113">Создание Razor веб-приложения</span><span class="sxs-lookup"><span data-stu-id="06f4c-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06f4c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06f4c-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="06f4c-115">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="06f4c-116">Присвойте проекту имя **APP1** , если вы хотите, чтобы оно соответствовало пространству имен примера кода для [скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="06f4c-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="06f4c-117">Выберите **ASP.NET Core веб-приложение** > **ОК**</span><span class="sxs-lookup"><span data-stu-id="06f4c-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="06f4c-118">Выберите **ASP.NET Core 3,0** в раскрывающемся списке.</span><span class="sxs-lookup"><span data-stu-id="06f4c-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="06f4c-119">Выберите **веб-приложение** > **ОК** .</span><span class="sxs-lookup"><span data-stu-id="06f4c-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="06f4c-120">Постройте и запустите проект.</span><span class="sxs-lookup"><span data-stu-id="06f4c-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="06f4c-121">В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="06f4c-122">Присвойте проекту имя **APP1** , если вы хотите, чтобы оно соответствовало пространству имен примера кода для [скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="06f4c-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="06f4c-123">Выберите **ASP.NET Core веб-приложение** > **ОК**</span><span class="sxs-lookup"><span data-stu-id="06f4c-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="06f4c-124">Выберите **ASP.NET Core 2,2** в раскрывающемся списке.</span><span class="sxs-lookup"><span data-stu-id="06f4c-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="06f4c-125">Выберите **веб-приложение** > **ОК** .</span><span class="sxs-lookup"><span data-stu-id="06f4c-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="06f4c-126">Постройте и запустите проект.</span><span class="sxs-lookup"><span data-stu-id="06f4c-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="06f4c-127">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="06f4c-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="06f4c-128">Запуск Identity механизма формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="06f4c-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06f4c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06f4c-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="06f4c-130">В **Обозреватель решений** щелкните правой кнопкой мыши проект > **Добавить**  >  **Новый** шаблонный элемент.</span><span class="sxs-lookup"><span data-stu-id="06f4c-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="06f4c-131">В левой области диалогового окна **Добавление шаблона** выберите **Identity**  >  **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="06f4c-132">В диалоговом окне **Добавить можно задать Identity** следующие параметры.</span><span class="sxs-lookup"><span data-stu-id="06f4c-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="06f4c-133">Выберите существующий файл макета  *~/пажес/шаред/_layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="06f4c-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="06f4c-134">Выберите следующие файлы для переопределения:</span><span class="sxs-lookup"><span data-stu-id="06f4c-134">Select the following files to override:</span></span>
    * <span data-ttu-id="06f4c-135">**Учетная запись или регистр**</span><span class="sxs-lookup"><span data-stu-id="06f4c-135">**Account/Register**</span></span>
    * <span data-ttu-id="06f4c-136">**Учетная запись/управление/индекс**</span><span class="sxs-lookup"><span data-stu-id="06f4c-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="06f4c-137">Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="06f4c-138">Примите тип ("имя_проекта **. Models. WebApp1Context** ", если проект называется " **APP1**").</span><span class="sxs-lookup"><span data-stu-id="06f4c-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="06f4c-139">Нажмите **+** кнопку, чтобы создать новый **класс пользователя**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="06f4c-140">Примите тип (**WebApp1User** , если проект называется "имя_проекта **") >** **добавить**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="06f4c-141">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="06f4c-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="06f4c-142">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="06f4c-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="06f4c-143">Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:</span><span class="sxs-lookup"><span data-stu-id="06f4c-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="06f4c-144">Добавьте ссылку на пакет в [Microsoft. VisualStudio. Web. стратегию. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) в файл проекта (с расширением CSPROJ).</span><span class="sxs-lookup"><span data-stu-id="06f4c-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="06f4c-145">Выполните следующую команду в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="06f4c-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="06f4c-146">Выполните следующую команду, чтобы вывести список Identity параметров шаблона:</span><span class="sxs-lookup"><span data-stu-id="06f4c-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="06f4c-147">В папке проекта запустите программу Identity формирования шаблонов:</span><span class="sxs-lookup"><span data-stu-id="06f4c-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="06f4c-148">Следуйте инструкциям в разделе [миграция, усеаусентикатион и макет](xref:security/authentication/scaffold-identity#efm) , чтобы выполнить следующие действия.</span><span class="sxs-lookup"><span data-stu-id="06f4c-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="06f4c-149">Создайте миграцию и обновите базу данных.</span><span class="sxs-lookup"><span data-stu-id="06f4c-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="06f4c-150">Добавлен `UseAuthentication` в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="06f4c-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="06f4c-151">Добавьте `<partial name="_LoginPartial" />` в файл макета.</span><span class="sxs-lookup"><span data-stu-id="06f4c-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="06f4c-152">Проверьте работу приложения:</span><span class="sxs-lookup"><span data-stu-id="06f4c-152">Test the app:</span></span>
  * <span data-ttu-id="06f4c-153">Регистрация пользователя</span><span class="sxs-lookup"><span data-stu-id="06f4c-153">Register a user</span></span>
  * <span data-ttu-id="06f4c-154">Выберите новое имя пользователя (рядом с ссылкой для **выхода** ).</span><span class="sxs-lookup"><span data-stu-id="06f4c-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="06f4c-155">Может потребоваться развернуть окно или выбрать значок панели навигации, чтобы отобразить имя пользователя и другие ссылки.</span><span class="sxs-lookup"><span data-stu-id="06f4c-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="06f4c-156">Перейдите на вкладку **личные данные** .</span><span class="sxs-lookup"><span data-stu-id="06f4c-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="06f4c-157">Нажмите кнопку **скачать** и проверите *PersonalData.js* файла.</span><span class="sxs-lookup"><span data-stu-id="06f4c-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="06f4c-158">Протестируйте кнопку **Удалить** , которая удаляет пользователя, выполнившего вход в систему.</span><span class="sxs-lookup"><span data-stu-id="06f4c-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="06f4c-159">Добавление пользовательских данных в базу данных Identity</span><span class="sxs-lookup"><span data-stu-id="06f4c-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="06f4c-160">Обновите `IdentityUser` производный класс с помощью пользовательских свойств.</span><span class="sxs-lookup"><span data-stu-id="06f4c-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="06f4c-161">Если вы назвали имя проекта Project, файл будет называться *Areas/ Identity /Data/WebApp1User.CS*.</span><span class="sxs-lookup"><span data-stu-id="06f4c-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="06f4c-162">Обновите файл, используя следующий код:</span><span class="sxs-lookup"><span data-stu-id="06f4c-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="06f4c-163">Свойства с атрибутом [персоналдата](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) :</span><span class="sxs-lookup"><span data-stu-id="06f4c-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="06f4c-164">Удаляется при вызове страницы *Areas/ Identity /Пажес/аккаунт/манаже/делетеперсоналдата.кштмл* Razor `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="06f4c-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="06f4c-165">Включается в Скачанные данные на странице *Areas/ Identity /Пажес/аккаунт/манаже/довнлоадперсоналдата.кштмл* Razor .</span><span class="sxs-lookup"><span data-stu-id="06f4c-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="06f4c-166">Обновление страницы "учетная запись/управление/индекс. cshtml"</span><span class="sxs-lookup"><span data-stu-id="06f4c-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="06f4c-167">Обновите `InputModel` *область в области/ Identity /Пажес/аккаунт/манаже/индекс.кштмл.КС* , дополнив выделение следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="06f4c-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="06f4c-168">Обновите *области/ Identity /Пажес/аккаунт/манаже/индекс.кштмл* со следующей выделенной разметкой:</span><span class="sxs-lookup"><span data-stu-id="06f4c-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="06f4c-169">Обновите *области/ Identity /Пажес/аккаунт/манаже/индекс.кштмл* со следующей выделенной разметкой:</span><span class="sxs-lookup"><span data-stu-id="06f4c-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="06f4c-170">Обновление страницы "учетная запись/регистрация. cshtml"</span><span class="sxs-lookup"><span data-stu-id="06f4c-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="06f4c-171">Обновите `InputModel` *область в области/ Identity /Пажес/аккаунт/регистер.кштмл.КС* , дополнив выделение следующим выделенным кодом:</span><span class="sxs-lookup"><span data-stu-id="06f4c-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="06f4c-172">Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* со следующей выделенной разметкой:</span><span class="sxs-lookup"><span data-stu-id="06f4c-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="06f4c-173">Обновите *области/ Identity /Пажес/аккаунт/регистер.кштмл* со следующей выделенной разметкой:</span><span class="sxs-lookup"><span data-stu-id="06f4c-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="06f4c-174">Выполните построение проекта.</span><span class="sxs-lookup"><span data-stu-id="06f4c-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="06f4c-175">Добавление миграции для настраиваемых данных пользователя</span><span class="sxs-lookup"><span data-stu-id="06f4c-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06f4c-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06f4c-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06f4c-177">В **консоли диспетчера пакетов** Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="06f4c-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="06f4c-178">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="06f4c-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="06f4c-179">Тестирование создания, просмотра, загрузки, удаления пользовательских данных пользователя</span><span class="sxs-lookup"><span data-stu-id="06f4c-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="06f4c-180">Проверьте работу приложения:</span><span class="sxs-lookup"><span data-stu-id="06f4c-180">Test the app:</span></span>

* <span data-ttu-id="06f4c-181">Регистрация нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="06f4c-181">Register a new user.</span></span>
* <span data-ttu-id="06f4c-182">Просмотр настраиваемых данных пользователя на `/Identity/Account/Manage` странице.</span><span class="sxs-lookup"><span data-stu-id="06f4c-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="06f4c-183">Скачайте и просмотрите персональные данные пользователей на `/Identity/Account/Manage/PersonalData` странице.</span><span class="sxs-lookup"><span data-stu-id="06f4c-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="06f4c-184">Добавление утверждений для Identity использования иусерклаимспринЦипалфактори<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="06f4c-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="06f4c-185">Этот раздел не является расширением предыдущего руководства.</span><span class="sxs-lookup"><span data-stu-id="06f4c-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="06f4c-186">Чтобы применить следующие действия к приложению, созданному с помощью учебника, см. [эту ошибку в GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span><span class="sxs-lookup"><span data-stu-id="06f4c-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="06f4c-187">Дополнительные утверждения можно добавить в с ASP.NET Core Identity помощью `IUserClaimsPrincipalFactory<T>` интерфейса.</span><span class="sxs-lookup"><span data-stu-id="06f4c-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="06f4c-188">Этот класс можно добавить в приложение в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="06f4c-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="06f4c-189">Добавьте пользовательскую реализацию класса следующим образом:</span><span class="sxs-lookup"><span data-stu-id="06f4c-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="06f4c-190">В демонстрационном коде используется `ApplicationUser` класс.</span><span class="sxs-lookup"><span data-stu-id="06f4c-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="06f4c-191">Этот класс добавляет `IsAdmin` свойство, которое используется для добавления дополнительного утверждения.</span><span class="sxs-lookup"><span data-stu-id="06f4c-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="06f4c-192">Класс `AdditionalUserClaimsPrincipalFactory` реализует интерфейс `UserClaimsPrincipalFactory`.</span><span class="sxs-lookup"><span data-stu-id="06f4c-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="06f4c-193">Новое утверждение роли добавляется в `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="06f4c-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="06f4c-194">Затем в приложении можно использовать дополнительное утверждение.</span><span class="sxs-lookup"><span data-stu-id="06f4c-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="06f4c-195">На Razor странице `IAuthorizationService` экземпляр можно использовать для доступа к значению утверждения.</span><span class="sxs-lookup"><span data-stu-id="06f4c-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
