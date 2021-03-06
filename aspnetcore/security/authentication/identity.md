---
title: Введение в Identity ASP.NET Core
author: rick-anderson
description: Используйте Identity с приложением ASP.NET Core. Узнайте, как устанавливать требования к паролю (Рекуиредигит, Рекуиредленгс, Рекуиредуникуечарс и др.).
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 4fa49f795b78b88e00bd32d04f74acd8689383b2
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394477"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="d705a-104">Введение в Identity ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d705a-105">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="d705a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d705a-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="d705a-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="d705a-107">— Это API, поддерживающий функцию входа в пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="d705a-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="d705a-108">Управляет пользователями, паролями, данными профилирования, ролями, утверждениями, маркерами, подтверждением электронной почты и т. д.</span><span class="sxs-lookup"><span data-stu-id="d705a-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="d705a-109">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="d705a-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d705a-110">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d705a-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="d705a-111">[ Identity Исходный код](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) доступен на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="d705a-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="d705a-112">[Формирование Identity шаблонов](xref:security/authentication/scaffold-identity) и просмотрите созданные файлы, чтобы проверить взаимодействие с шаблоном Identity .</span><span class="sxs-lookup"><span data-stu-id="d705a-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="d705a-113">Identity обычно настраивается с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="d705a-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d705a-114">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="d705a-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d705a-115">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="d705a-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d705a-116">Примечание. шаблоны считают имя пользователя и адрес электронной почты одинаковыми для пользователей.</span><span class="sxs-lookup"><span data-stu-id="d705a-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="d705a-117">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе [дальнейшие действия](#next).</span><span class="sxs-lookup"><span data-stu-id="d705a-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="d705a-118">[Платформа Microsoft Identity Platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="d705a-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="d705a-119">Развитие платформы разработки Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="d705a-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="d705a-120">Не связано с ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="d705a-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="d705a-121">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([Загрузка](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d705a-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d705a-122">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="d705a-122">Create a Web app with authentication</span></span>

<span data-ttu-id="d705a-123">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="d705a-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d705a-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d705a-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d705a-125">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="d705a-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d705a-126">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d705a-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d705a-127">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="d705a-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d705a-128">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d705a-128">Click **OK**.</span></span>
* <span data-ttu-id="d705a-129">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="d705a-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d705a-130">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d705a-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d705a-131">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="d705a-132">Предыдущая команда создает Razor веб-приложение с помощью SQLite.</span><span class="sxs-lookup"><span data-stu-id="d705a-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="d705a-133">Чтобы создать веб-приложение с помощью LocalDB, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d705a-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="d705a-134">Созданный проект предоставляет [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor библиотеку классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d705a-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d705a-135">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="d705a-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d705a-136">Пример:</span><span class="sxs-lookup"><span data-stu-id="d705a-136">For example:</span></span>

* <span data-ttu-id="d705a-137">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="d705a-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="d705a-138">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="d705a-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d705a-139">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="d705a-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d705a-140">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="d705a-140">Apply migrations</span></span>

<span data-ttu-id="d705a-141">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="d705a-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d705a-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d705a-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d705a-143">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="d705a-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="d705a-144">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d705a-145">Миграция не требуется на этом этапе при использовании SQLite.</span><span class="sxs-lookup"><span data-stu-id="d705a-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d705a-146">Для LocalDB выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d705a-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d705a-147">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="d705a-147">Test Register and Login</span></span>

<span data-ttu-id="d705a-148">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="d705a-148">Run the app and register a user.</span></span> <span data-ttu-id="d705a-149">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="d705a-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d705a-150">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="d705a-150">Configure Identity services</span></span>

<span data-ttu-id="d705a-151">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d705a-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d705a-152">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="d705a-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="d705a-153">Выделенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d705a-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="d705a-154">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d705a-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d705a-155">Identity включается путем вызова метода <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="d705a-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="d705a-156">`UseAuthentication` добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="d705a-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="d705a-157">Приведенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d705a-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="d705a-158">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d705a-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d705a-159">Identity включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d705a-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d705a-160">`UseAuthentication` добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="d705a-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d705a-161">Созданное шаблоном приложение не использует [авторизацию](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="d705a-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="d705a-162">`app.UseAuthorization` включается, чтобы обеспечить его добавление в правильном порядке, если приложение добавляет авторизацию.</span><span class="sxs-lookup"><span data-stu-id="d705a-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="d705a-163">`UseRouting`, `UseAuthentication` , `UseAuthorization` и `UseEndpoints` должны вызываться в порядке, показанном в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="d705a-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="d705a-164">Дополнительные сведения о `IdentityOptions` и см `Startup` . в разделе <xref:Microsoft.AspNetCore.Identity.IdentityOptions> и [запуске приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d705a-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="d705a-165">Регистр шаблонов, вход, выход и Регистерконфирматион</span><span class="sxs-lookup"><span data-stu-id="d705a-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d705a-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d705a-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d705a-167">Добавьте `Register` файлы, `Login` , `LogOut` и `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="d705a-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="d705a-168">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="d705a-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d705a-169">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d705a-170">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="d705a-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d705a-171">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="d705a-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="d705a-172">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="d705a-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d705a-173">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="d705a-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="d705a-174">Дополнительные сведения о формировании шаблонов Identity см. в статье [удостоверение шаблона в Razor проекте с авторизацией](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="d705a-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d705a-175">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="d705a-175">Examine Register</span></span>

<span data-ttu-id="d705a-176">Когда пользователь нажимает кнопку **Register** на `Register` странице, `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="d705a-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d705a-177">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="d705a-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="d705a-178">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="d705a-178">Log in</span></span>

<span data-ttu-id="d705a-179">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="d705a-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="d705a-180">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="d705a-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d705a-181">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="d705a-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d705a-182">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="d705a-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d705a-183">`PasswordSignInAsync` метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="d705a-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d705a-184">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="d705a-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d705a-185">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="d705a-185">Log out</span></span>

<span data-ttu-id="d705a-186">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="d705a-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="d705a-187">В приведенном выше коде код `return RedirectToPage();` должен быть перенаправлением, чтобы браузер выполнял новый запрос и удостоверение для пользователя обновляется.</span><span class="sxs-lookup"><span data-stu-id="d705a-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="d705a-188">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в cookie .</span><span class="sxs-lookup"><span data-stu-id="d705a-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d705a-189">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d705a-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="d705a-190">Проверьте Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-190">Test Identity</span></span>

<span data-ttu-id="d705a-191">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="d705a-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d705a-192">Чтобы протестировать Identity , добавьте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d705a-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d705a-193">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="d705a-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d705a-194">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="d705a-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d705a-195">Просматриваем Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-195">Explore Identity</span></span>

<span data-ttu-id="d705a-196">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="d705a-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d705a-197">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="d705a-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d705a-198">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="d705a-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="d705a-199">Identity Компонента</span><span class="sxs-lookup"><span data-stu-id="d705a-199">Identity Components</span></span>

<span data-ttu-id="d705a-200">Все Identity зависимые пакеты NuGet включены в [ASP.NET Core общую платформу](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span><span class="sxs-lookup"><span data-stu-id="d705a-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="d705a-201">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="d705a-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d705a-202">Этот пакет содержит основной набор интерфейсов для ASP.NET Core Identity и включен в `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="d705a-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d705a-203">Миграция в ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d705a-204">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d705a-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d705a-205">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="d705a-205">Setting password strength</span></span>

<span data-ttu-id="d705a-206">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="d705a-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d705a-207">Адддефаулт Identity и добавитьIdentity</span><span class="sxs-lookup"><span data-stu-id="d705a-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d705a-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d705a-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d705a-209">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="d705a-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d705a-210">Дополнительные сведения см. в разделе [адддефаулт Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d705a-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="d705a-211">Запретить публикацию статических Identity ресурсов</span><span class="sxs-lookup"><span data-stu-id="d705a-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="d705a-212">Чтобы предотвратить публикацию статических Identity ресурсов (таблиц стилей и файлов JavaScript для Identity пользовательского интерфейса) в корневом каталоге веб-сайта, добавьте следующее `ResolveStaticWebAssetsInputsDependsOn` свойство и `RemoveIdentityAssets` целевой объект в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="d705a-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="d705a-213">Next Steps</span><span class="sxs-lookup"><span data-stu-id="d705a-213">Next Steps</span></span>

* <span data-ttu-id="d705a-214">[Исходный код ASP.NET Core Identity](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="d705a-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="d705a-215">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="d705a-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d705a-216">Настройка Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d705a-217">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="d705a-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d705a-218">ASP.NET Core Identity — Это система членства, которая добавляет функции входа в ASP.NET Core приложения.</span><span class="sxs-lookup"><span data-stu-id="d705a-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="d705a-219">Пользователи могут создать учетную запись с данными входа, хранящимися в, Identity или использовать внешний поставщик входа.</span><span class="sxs-lookup"><span data-stu-id="d705a-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d705a-220">Поддерживаемые внешние поставщики входа включают [Facebook, Google, учетную запись Майкрософт и Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d705a-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d705a-221">Identity можно настроить с помощью SQL Server базы данных для хранения имен пользователей, паролей и данных профилей.</span><span class="sxs-lookup"><span data-stu-id="d705a-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d705a-222">Кроме того, можно использовать еще одно постоянное хранилище, например хранилище таблиц Azure.</span><span class="sxs-lookup"><span data-stu-id="d705a-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d705a-223">[Просмотр или скачивание образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([Загрузка](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d705a-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d705a-224">В этом разделе вы узнаете, как использовать Identity для регистрации, входа и выхода пользователя.</span><span class="sxs-lookup"><span data-stu-id="d705a-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d705a-225">Более подробные инструкции по созданию приложений, использующих Identity , см. в разделе дальнейшие действия в конце этой статьи.</span><span class="sxs-lookup"><span data-stu-id="d705a-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d705a-226">Адддефаулт Identity и добавитьIdentity</span><span class="sxs-lookup"><span data-stu-id="d705a-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d705a-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> было введено в ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="d705a-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d705a-228">Вызов `AddDefaultIdentity` аналогичен вызову следующего:</span><span class="sxs-lookup"><span data-stu-id="d705a-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d705a-229">Дополнительные сведения см. в разделе [адддефаулт Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d705a-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d705a-230">Создание веб-приложения с проверкой подлинности</span><span class="sxs-lookup"><span data-stu-id="d705a-230">Create a Web app with authentication</span></span>

<span data-ttu-id="d705a-231">Создание ASP.NET Core проекта веб-приложения с учетными записями отдельных пользователей.</span><span class="sxs-lookup"><span data-stu-id="d705a-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d705a-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d705a-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d705a-233">Выберите **файл** > **создать** > **проект**.</span><span class="sxs-lookup"><span data-stu-id="d705a-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d705a-234">Выберите **Новое веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="d705a-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d705a-235">Присвойте проекту имя **APP1** , которое будет иметь то же пространство имен, что и загружаемый проект.</span><span class="sxs-lookup"><span data-stu-id="d705a-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d705a-236">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d705a-236">Click **OK**.</span></span>
* <span data-ttu-id="d705a-237">Выберите ASP.NET Core **веб-приложение**, а затем щелкните **изменить проверку подлинности**.</span><span class="sxs-lookup"><span data-stu-id="d705a-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d705a-238">Выберите **учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="d705a-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d705a-239">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="d705a-240">Созданный проект предоставляет [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor библиотеку классов](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="d705a-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d705a-241">Identity Razor Библиотека классов предоставляет конечные точки с `Identity` областью.</span><span class="sxs-lookup"><span data-stu-id="d705a-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d705a-242">Пример:</span><span class="sxs-lookup"><span data-stu-id="d705a-242">For example:</span></span>

* <span data-ttu-id="d705a-243">/Identity/аккаунт/логин</span><span class="sxs-lookup"><span data-stu-id="d705a-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="d705a-244">/Identity/аккаунт/логаут</span><span class="sxs-lookup"><span data-stu-id="d705a-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d705a-245">/Identity/аккаунт/манаже</span><span class="sxs-lookup"><span data-stu-id="d705a-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d705a-246">Применение миграции</span><span class="sxs-lookup"><span data-stu-id="d705a-246">Apply migrations</span></span>

<span data-ttu-id="d705a-247">Примените миграции, чтобы инициализировать базу данных.</span><span class="sxs-lookup"><span data-stu-id="d705a-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d705a-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d705a-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d705a-249">Выполните следующую команду в консоли диспетчера пакетов (PMC):</span><span class="sxs-lookup"><span data-stu-id="d705a-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d705a-250">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d705a-251">Проверка регистра и вход в систему</span><span class="sxs-lookup"><span data-stu-id="d705a-251">Test Register and Login</span></span>

<span data-ttu-id="d705a-252">Запустите приложение и зарегистрируйте пользователя.</span><span class="sxs-lookup"><span data-stu-id="d705a-252">Run the app and register a user.</span></span> <span data-ttu-id="d705a-253">В зависимости от размера экрана может потребоваться выбрать переключатель навигации, чтобы просмотреть ссылки **Регистрация** и **Вход** .</span><span class="sxs-lookup"><span data-stu-id="d705a-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d705a-254">Настройка Identity служб</span><span class="sxs-lookup"><span data-stu-id="d705a-254">Configure Identity services</span></span>

<span data-ttu-id="d705a-255">Службы добавляются в `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d705a-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d705a-256">По стандартному шаблону сначала вызываются все методы `Add{Service}`, а затем все методы `services.Configure{Service}`.</span><span class="sxs-lookup"><span data-stu-id="d705a-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="d705a-257">Приведенный выше код настраивается Identity со значениями параметров по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d705a-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="d705a-258">Доступ к службам предоставляется приложению посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d705a-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d705a-259">Identity включается путем вызова [усеаусентикатион](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="d705a-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d705a-260">`UseAuthentication` добавляет по [промежуточного слоя](xref:fundamentals/middleware/index) проверки подлинности в конвейер запросов.</span><span class="sxs-lookup"><span data-stu-id="d705a-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="d705a-261">Дополнительные сведения см. в статье [ Identity класс Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) и [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d705a-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d705a-262">Регистрация, вход и выход из шаблона формирования шаблонов</span><span class="sxs-lookup"><span data-stu-id="d705a-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="d705a-263">Соблюдайте [идентификатор шаблона в Razor проекте с](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) инструкциями по авторизации, чтобы создать код, приведенный в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="d705a-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d705a-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d705a-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d705a-265">Добавьте файлы Register, login и LogOut.</span><span class="sxs-lookup"><span data-stu-id="d705a-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d705a-266">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d705a-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d705a-267">Если вы создали проект с именем " **APP1**", выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="d705a-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d705a-268">В противном случае используйте правильное пространство имен для `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="d705a-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

<span data-ttu-id="d705a-269">При использовании SQLite `--useSqLite` необходимо указать:</span><span class="sxs-lookup"><span data-stu-id="d705a-269">When using SQLite, `--useSqLite` must be specified:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

<span data-ttu-id="d705a-270">В SQL Express используйте следующие команды:</span><span class="sxs-lookup"><span data-stu-id="d705a-270">With SQL Express, use the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d705a-271">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="d705a-271">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d705a-272">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки, как показано в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="d705a-272">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d705a-273">Проверка регистра</span><span class="sxs-lookup"><span data-stu-id="d705a-273">Examine Register</span></span>

<span data-ttu-id="d705a-274">Когда пользователь щелкает ссылку **Register** , `RegisterModel.OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="d705a-274">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d705a-275">Пользователь создается с помощью [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) для `_userManager` объекта:</span><span class="sxs-lookup"><span data-stu-id="d705a-275">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="d705a-276">Если пользователь успешно создан, пользователь входит в систему с помощью вызова `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="d705a-276">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d705a-277">**Примечание.** Инструкции по предотвращению немедленного входа при регистрации см. в статье [Подтверждение учетной записи](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="d705a-277">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d705a-278">Вход в систему</span><span class="sxs-lookup"><span data-stu-id="d705a-278">Log in</span></span>

<span data-ttu-id="d705a-279">Форма входа отображается в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="d705a-279">The Login form is displayed when:</span></span>

* <span data-ttu-id="d705a-280">Выбрана ссылка для **входа** .</span><span class="sxs-lookup"><span data-stu-id="d705a-280">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d705a-281">Пользователь пытается получить доступ к странице с ограниченным доступом, которая не авторизована для доступа, **или** если система не прошла проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="d705a-281">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d705a-282">При отправке формы на странице входа `OnPostAsync` вызывается действие.</span><span class="sxs-lookup"><span data-stu-id="d705a-282">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d705a-283">`PasswordSignInAsync` метод вызывается для `_signInManager` объекта.</span><span class="sxs-lookup"><span data-stu-id="d705a-283">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d705a-284">Сведения о принятии решений об авторизации см. в разделе <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="d705a-284">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d705a-285">выход из системы;</span><span class="sxs-lookup"><span data-stu-id="d705a-285">Log out</span></span>

<span data-ttu-id="d705a-286">Ссылка **выхода** вызывает `LogoutModel.OnPost` действие.</span><span class="sxs-lookup"><span data-stu-id="d705a-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="d705a-287">[Сигнаутасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) очищает утверждения пользователя, хранящиеся в cookie .</span><span class="sxs-lookup"><span data-stu-id="d705a-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d705a-288">Параметр POST указан в *страницах Pages/Shared/_LoginPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d705a-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="d705a-289">Проверьте Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-289">Test Identity</span></span>

<span data-ttu-id="d705a-290">Шаблоны веб-проектов по умолчанию разрешают анонимный доступ к домашним страницам.</span><span class="sxs-lookup"><span data-stu-id="d705a-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d705a-291">Чтобы протестировать Identity , добавьте [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) на страницу конфиденциальность.</span><span class="sxs-lookup"><span data-stu-id="d705a-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d705a-292">Если вы вошли в систему, выйдите из нее. Запустите приложение и щелкните ссылку **Конфиденциальность** .</span><span class="sxs-lookup"><span data-stu-id="d705a-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d705a-293">Вы перейдете на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="d705a-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d705a-294">Просматриваем Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-294">Explore Identity</span></span>

<span data-ttu-id="d705a-295">IdentityБолее подробное изучение:</span><span class="sxs-lookup"><span data-stu-id="d705a-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d705a-296">Создание полного источника идентификатора пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="d705a-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d705a-297">Изучите источник каждой страницы и пошаговое выполнение отладчика.</span><span class="sxs-lookup"><span data-stu-id="d705a-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="d705a-298">Identity Компонента</span><span class="sxs-lookup"><span data-stu-id="d705a-298">Identity Components</span></span>

<span data-ttu-id="d705a-299">Все Identity зависимые пакеты NuGet включены в пакет [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d705a-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d705a-300">Основным пакетом для Identity является [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="d705a-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d705a-301">Этот пакет содержит основной набор интерфейсов для ASP.NET Core Identity и включен в `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="d705a-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d705a-302">Миграция в ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d705a-303">Дополнительные сведения и рекомендации по переносу существующего Identity хранилища см. в статье [Миграция Identity проверки подлинности и ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d705a-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d705a-304">Установка силы пароля</span><span class="sxs-lookup"><span data-stu-id="d705a-304">Setting password strength</span></span>

<span data-ttu-id="d705a-305">См. раздел [Конфигурация](#pw) для примера, который устанавливает минимальные требования к паролю.</span><span class="sxs-lookup"><span data-stu-id="d705a-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d705a-306">Next Steps</span><span class="sxs-lookup"><span data-stu-id="d705a-306">Next Steps</span></span>

* <span data-ttu-id="d705a-307">Сведения о настройке с помощью SQLite см. в [этой статье о проблемах GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="d705a-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="d705a-308">Настройка Identity</span><span class="sxs-lookup"><span data-stu-id="d705a-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
