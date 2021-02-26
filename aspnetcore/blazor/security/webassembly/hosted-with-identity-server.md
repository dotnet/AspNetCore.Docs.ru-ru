---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью IdentityServer
author: guardrex
description: Узнайте, как защитить размещенное приложение ASP.NET Core Blazor WebAssembly с помощью Identity Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: c74711c10fe399718600f879c3d9151bfb1abd42
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281006"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="2126d-103">Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="2126d-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="2126d-104">В этой статье объясняется, как создать новое [размещенное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует [IdentityServer](https://identityserver.io/) для аутентификации пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="2126d-104">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="2126d-105">Чтобы настроить изолированное или размещенное приложение Blazor WebAssembly для использования существующего внешнего экземпляра Identity Server, следуйте рекомендациям в статье <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="2126d-105">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2126d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2126d-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2126d-107">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2126d-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="2126d-108">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="2126d-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="2126d-109">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2126d-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="2126d-110">Установите флажок **С размещением в ASP.NET Core** в разделе **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="2126d-110">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="2126d-111">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2126d-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="2126d-112">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке, укажите механизм аутентификации `Individual` с параметром `-au|--auth` для хранения пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2126d-112">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="2126d-113">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="2126d-113">Placeholder</span></span>  | <span data-ttu-id="2126d-114">Пример</span><span class="sxs-lookup"><span data-stu-id="2126d-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="2126d-115">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="2126d-116">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2126d-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2126d-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2126d-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2126d-118">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2126d-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="2126d-119">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="2126d-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="2126d-120">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2126d-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="2126d-121">Установите флажок **С размещением в ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="2126d-121">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="2126d-122">Конфигурация приложения *`Server`*</span><span class="sxs-lookup"><span data-stu-id="2126d-122">*`Server`* app configuration</span></span>

<span data-ttu-id="2126d-123">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2126d-123">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="2126d-124">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="2126d-124">Startup class</span></span>

<span data-ttu-id="2126d-125">В классе `Startup` представлены следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="2126d-125">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="2126d-126">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2126d-126">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="2126d-127">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="2126d-127">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="2126d-128">IdentityServer с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="2126d-128">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="2126d-129">Аутентификация с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="2126d-129">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="2126d-130">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2126d-130">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="2126d-131">ПО промежуточного слоя IdentityServer предоставляет конечные точки OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="2126d-131">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="2126d-132">ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="2126d-132">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="2126d-133">ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="2126d-133">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthorization();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="2126d-134">Служба приложений Azure в Linux</span><span class="sxs-lookup"><span data-stu-id="2126d-134">Azure App Service on Linux</span></span>

<span data-ttu-id="2126d-135">При развертывании в Службе приложений Azure в Linux издателя нужно указать явно.</span><span class="sxs-lookup"><span data-stu-id="2126d-135">Specify the issuer explicitly when deploying to Azure App Service on Linux.</span></span> <span data-ttu-id="2126d-136">Для получения дополнительной информации см. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="2126d-136">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

### <a name="addapiauthorization"></a><span data-ttu-id="2126d-137">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="2126d-137">AddApiAuthorization</span></span>

<span data-ttu-id="2126d-138">Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2126d-138">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="2126d-139">IdentityServer — это функциональная и расширяемая платформа для повышения уровня безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="2126d-139">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="2126d-140">IdentityServer указывает на ненужную сложность в некоторых распространенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="2126d-140">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="2126d-141">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки.</span><span class="sxs-lookup"><span data-stu-id="2126d-141">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="2126d-142">Если нужно изменить требования к аутентификации, можно воспользоваться широкими возможностями IdentityServer для настройки аутентификации в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-142">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="2126d-143">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="2126d-143">AddIdentityServerJwt</span></span>

<span data-ttu-id="2126d-144">Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2126d-144">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="2126d-145">Политика настроена так, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов Identity `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="2126d-145">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="2126d-146"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="2126d-146">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="2126d-147">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="2126d-147">Additionally, this method:</span></span>

* <span data-ttu-id="2126d-148">Регистрирует ресурс API `{APPLICATION NAME}API` в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="2126d-148">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="2126d-149">Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-149">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="2126d-150">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="2126d-150">WeatherForecastController</span></span>

<span data-ttu-id="2126d-151">В `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) к классу применяется [атрибут `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="2126d-151">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) is applied to the class.</span></span> <span data-ttu-id="2126d-152">Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="2126d-152">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="2126d-153">Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span><span class="sxs-lookup"><span data-stu-id="2126d-153">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="2126d-154">Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="2126d-154">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="2126d-155">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="2126d-155">ApplicationDbContext</span></span>

<span data-ttu-id="2126d-156">В `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="2126d-156">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="2126d-157">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="2126d-157"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="2126d-158">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов Identity <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы Identity путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="2126d-158">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="2126d-159">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="2126d-159">OidcConfigurationController</span></span>

<span data-ttu-id="2126d-160">В `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) выполняется подготовка конечной точки клиента для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="2126d-160">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="2126d-161">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="2126d-161">App settings</span></span>

<span data-ttu-id="2126d-162">В файле параметров приложения (`appsettings.json`) в корневом каталоге проекта в разделе `IdentityServer` описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="2126d-162">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="2126d-163">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="2126d-163">In the following example, there's a single client.</span></span> <span data-ttu-id="2126d-164">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="2126d-164">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="2126d-165">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-165">The profile indicates the app type being configured.</span></span> <span data-ttu-id="2126d-166">Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="2126d-166">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="2126d-167">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="2126d-167">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="2126d-168">Конфигурация приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="2126d-168">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2126d-169">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2126d-169">Authentication package</span></span>

<span data-ttu-id="2126d-170">Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-170">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="2126d-171">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="2126d-171">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2126d-172">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="2126d-172">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="2126d-173">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="2126d-173">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="2126d-174">Конфигурация `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="2126d-174">`HttpClient` configuration</span></span>

<span data-ttu-id="2126d-175">В `Program.Main` (`Program.cs`) именованный <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) настроен для предоставления экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при отправке запросов к API сервера:</span><span class="sxs-lookup"><span data-stu-id="2126d-175">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="2126d-176">Если вы хотите настроить приложение Blazor WebAssembly на использование существующего экземпляра IdentityServer, который не является частью размещенного решения Blazor, измените регистрацию базового адреса <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) на URL-адрес конечной точки авторизации API серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-176">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="2126d-177">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="2126d-177">API authorization support</span></span>

<span data-ttu-id="2126d-178">Поддержка проверки подлинности пользователей подключается в контейнере службы с помощью метода расширения, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="2126d-178">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="2126d-179">Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="2126d-179">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="2126d-180">По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="2126d-180">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="2126d-181">По соглашению в качестве идентификатора клиента используется имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-181">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="2126d-182">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="2126d-182">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="2126d-183">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="2126d-183">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="2126d-184">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="2126d-184">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="2126d-185">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="2126d-185">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="2126d-186">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2126d-186">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="2126d-187">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2126d-187">LoginDisplay component</span></span>

<span data-ttu-id="2126d-188">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="2126d-188">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="2126d-189">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="2126d-189">For authenticated users:</span></span>
  * <span data-ttu-id="2126d-190">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="2126d-190">Displays the current user name.</span></span>
  * <span data-ttu-id="2126d-191">Отображает ссылку на страницу профиля пользователя в ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="2126d-191">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="2126d-192">Отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-192">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="2126d-193">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="2126d-193">For anonymous users:</span></span>
  * <span data-ttu-id="2126d-194">Предоставляет возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="2126d-194">Offers the option to register.</span></span>
  * <span data-ttu-id="2126d-195">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="2126d-195">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="2126d-196">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2126d-196">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="2126d-197">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="2126d-197">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="2126d-198">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2126d-198">Run the app</span></span>

<span data-ttu-id="2126d-199">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="2126d-199">Run the app from the Server project.</span></span> <span data-ttu-id="2126d-200">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="2126d-200">When using Visual Studio, either:</span></span>

* <span data-ttu-id="2126d-201">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="2126d-201">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="2126d-202">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="2126d-202">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="2126d-203">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="2126d-203">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="2126d-204">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="2126d-204">Custom user factory</span></span>

<span data-ttu-id="2126d-205">В приложении *`Client`* создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="2126d-205">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="2126d-206">Identity Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`.</span><span class="sxs-lookup"><span data-stu-id="2126d-206">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="2126d-207">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="2126d-207">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="2126d-208">Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="2126d-208">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="2126d-209">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="2126d-209">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="2126d-210">В приложении *`Client`* зарегистрируйте фабрику в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="2126d-210">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="2126d-211">В приложении *`Server`* вызовите <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в построителе Identity, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="2126d-211">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="2126d-212">Настройка Identity Server</span><span class="sxs-lookup"><span data-stu-id="2126d-212">Configure Identity Server</span></span>

<span data-ttu-id="2126d-213">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="2126d-213">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="2126d-214">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="2126d-214">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="2126d-215">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="2126d-215">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="2126d-216">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="2126d-216">API authorization options</span></span>

<span data-ttu-id="2126d-217">В приложении *`Server`* :</span><span class="sxs-lookup"><span data-stu-id="2126d-217">In the *`Server`* app:</span></span>

* <span data-ttu-id="2126d-218">Настройте Identity Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="2126d-218">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="2126d-219">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="2126d-219">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="2126d-220">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="2126d-220">Profile Service</span></span>

<span data-ttu-id="2126d-221">В приложении *`Server`* создайте реализацию `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="2126d-221">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="2126d-222">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="2126d-222">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="2126d-223">В приложении *`Server`* зарегистрируйте службу профиля в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2126d-223">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="2126d-224">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="2126d-224">Use authorization mechanisms</span></span>

<span data-ttu-id="2126d-225">На этом этапе подходы с авторизацией компонентов в приложении *`Client`* являются функциональными.</span><span class="sxs-lookup"><span data-stu-id="2126d-225">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="2126d-226">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="2126d-226">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="2126d-227">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="2126d-227">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="2126d-228">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="2126d-228">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="2126d-229">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="2126d-229">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="2126d-230">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="2126d-230">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="2126d-231">Для `User.Identity.Name` в приложении *`Client`* указывается имя пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="2126d-231">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain-and-certificate"></a><span data-ttu-id="2126d-232">Размещение в Службе приложений Azure с использованием личного домена и сертификата</span><span class="sxs-lookup"><span data-stu-id="2126d-232">Host in Azure App Service with a custom domain and certificate</span></span>

<span data-ttu-id="2126d-233">В следующем руководстве описывается:</span><span class="sxs-lookup"><span data-stu-id="2126d-233">The following guidance explains:</span></span>

* <span data-ttu-id="2126d-234">как развернуть размещенное приложение Blazor WebAssembly с Identity Server в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) с использованием личного домена;</span><span class="sxs-lookup"><span data-stu-id="2126d-234">How to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>
* <span data-ttu-id="2126d-235">как создать и использовать TLS-сертификат для обмена данными по протоколу HTTPS с браузерами.</span><span class="sxs-lookup"><span data-stu-id="2126d-235">How to create and use a TLS certificate for HTTPS protocol communication with browsers.</span></span> <span data-ttu-id="2126d-236">Хотя руководство посвящено использованию сертификата с личным доменом, рекомендации также можно применять при работе с доменом приложений Azure по умолчанию, например `contoso.azurewebsites.net`.</span><span class="sxs-lookup"><span data-stu-id="2126d-236">Although the guidance focuses on using the certificate with a custom domain, the guidance is equally applicable to using a default Azure Apps domain, for example `contoso.azurewebsites.net`.</span></span>

<span data-ttu-id="2126d-237">В этом сценарии размещения **не** используйте один и тот же сертификат для [ключа подписывания токена Identity Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) и защищенного обмена данными по протоколу HTTPS между сайтом и браузерами.</span><span class="sxs-lookup"><span data-stu-id="2126d-237">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="2126d-238">Использование разных сертификатов в этих целях рекомендуется из соображений безопасности, так как позволяет изолировать закрытые ключи разного назначения.</span><span class="sxs-lookup"><span data-stu-id="2126d-238">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="2126d-239">Управление TLS-сертификатами для обмена данными с браузерами производится независимо и не влияет на подписывание маркеров Identity Server.</span><span class="sxs-lookup"><span data-stu-id="2126d-239">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="2126d-240">Когда [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) предоставляет сертификат приложению Службы приложений для привязки к личному домену, Identity Server не может получить тот же сертификат из Azure Key Vault для подписывания токена.</span><span class="sxs-lookup"><span data-stu-id="2126d-240">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="2126d-241">Хотя Identity Server можно настроить так, чтобы он использовал тот же TLS-сертификат по физическому пути, размещать сертификаты безопасности в системе управления версиями **не рекомендуется, в большинстве случаев этого следует избегать**.</span><span class="sxs-lookup"><span data-stu-id="2126d-241">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="2126d-242">Далее в Azure Key Vault создается самозаверяющий сертификат исключительно для подписывания токена Identity Server.</span><span class="sxs-lookup"><span data-stu-id="2126d-242">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="2126d-243">Конфигурация Identity Server использует сертификат из хранилища ключей посредством хранилища сертификатов `My` > `CurrentUser` приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-243">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="2126d-244">Другие сертификаты, используемые для трафика HTTPS личных доменов, создаются и настраиваются отдельно от сертификата подписывания Identity Server.</span><span class="sxs-lookup"><span data-stu-id="2126d-244">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="2126d-245">Чтобы настроить приложение, Службу приложений Azure и Azure Key Vault для размещения в личном домене с HTTPS, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="2126d-245">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="2126d-246">Создайте [план Службы приложений](/azure/app-service/overview-hosting-plans) уровня `Basic B1` или выше.</span><span class="sxs-lookup"><span data-stu-id="2126d-246">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="2126d-247">Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.</span><span class="sxs-lookup"><span data-stu-id="2126d-247">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="2126d-248">Создайте PFX-сертификат для безопасного обмена данными между сайтом и браузером (по протоколу HTTPS) с полным доменным именем (FQDN) сайта, которое контролирует организация (например, `www.contoso.com`), в качестве общего имени.</span><span class="sxs-lookup"><span data-stu-id="2126d-248">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="2126d-249">Создайте сертификат с указанными ниже параметрами.</span><span class="sxs-lookup"><span data-stu-id="2126d-249">Create the certificate with:</span></span>
   * <span data-ttu-id="2126d-250">Ключ использует:</span><span class="sxs-lookup"><span data-stu-id="2126d-250">Key uses</span></span>
     * <span data-ttu-id="2126d-251">проверку цифровой подписи (`digitalSignature`);</span><span class="sxs-lookup"><span data-stu-id="2126d-251">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="2126d-252">шифрование ключей (`keyEncipherment`).</span><span class="sxs-lookup"><span data-stu-id="2126d-252">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="2126d-253">Расширенное использование ключа:</span><span class="sxs-lookup"><span data-stu-id="2126d-253">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="2126d-254">проверка подлинности клиента (1.3.6.1.5.5.7.3.2);</span><span class="sxs-lookup"><span data-stu-id="2126d-254">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="2126d-255">проверка подлинности сервера (1.3.6.1.5.5.7.3.1).</span><span class="sxs-lookup"><span data-stu-id="2126d-255">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="2126d-256">Чтобы создать сертификат, используйте один из следующих подходов или любое другое подходящее средство или веб-службу.</span><span class="sxs-lookup"><span data-stu-id="2126d-256">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="2126d-257">Хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="2126d-257">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="2126d-258">MakeCert в Windows</span><span class="sxs-lookup"><span data-stu-id="2126d-258">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="2126d-259">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="2126d-259">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="2126d-260">Запишите пароль, который будет использоваться позже для импорта сертификата в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="2126d-260">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="2126d-261">Дополнительные сведения о сертификатах Azure Key Vault см. в статье [Azure Key Vault. Сертификаты](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="2126d-261">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="2126d-262">Создайте хранилище Azure Key Vault или используйте уже имеющееся в подписке Azure.</span><span class="sxs-lookup"><span data-stu-id="2126d-262">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="2126d-263">В области **Сертификаты** хранилища ключей импортируйте сертификат сайта PFX.</span><span class="sxs-lookup"><span data-stu-id="2126d-263">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="2126d-264">Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-264">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="2126d-265">В Azure Key Vault создайте самозаверяющий сертификат для подписывания токена Identity Server.</span><span class="sxs-lookup"><span data-stu-id="2126d-265">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="2126d-266">Укажите **имя сертификата** и **субъект**.</span><span class="sxs-lookup"><span data-stu-id="2126d-266">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="2126d-267">**Субъект** указывается в формате `CN={COMMON NAME}`, где заполнитель `{COMMON NAME}` — это общее имя сертификата.</span><span class="sxs-lookup"><span data-stu-id="2126d-267">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="2126d-268">Общим именем может быть любая буквенно-цифровая строка.</span><span class="sxs-lookup"><span data-stu-id="2126d-268">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="2126d-269">Например, `CN=IdentityServerSigning` — допустимый **субъект** сертификата.</span><span class="sxs-lookup"><span data-stu-id="2126d-269">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="2126d-270">В разделе **Расширенная настройка политик** оставьте значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2126d-270">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="2126d-271">Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-271">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="2126d-272">Перейдите к Службе приложений Azure на портале Azure и создайте Службу приложений со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="2126d-272">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="2126d-273">**Опубликовать** — `Code`;</span><span class="sxs-lookup"><span data-stu-id="2126d-273">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="2126d-274">**Стек времени выполнения** — среда выполнения приложения;</span><span class="sxs-lookup"><span data-stu-id="2126d-274">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="2126d-275">**Номер SKU и размер** — уровень Службы приложений не ниже `Basic B1`.</span><span class="sxs-lookup"><span data-stu-id="2126d-275">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="2126d-276">Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.</span><span class="sxs-lookup"><span data-stu-id="2126d-276">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="2126d-277">После того как в Azure будет создана Служба приложений, откройте **конфигурацию приложения** и добавьте новый параметр, указав записанные ранее отпечатки сертификатов.</span><span class="sxs-lookup"><span data-stu-id="2126d-277">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="2126d-278">Ключ параметра — `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="2126d-278">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="2126d-279">В значении параметра разделяйте отпечатки запятыми, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="2126d-279">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="2126d-280">Ключ: `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="2126d-280">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="2126d-281">Значение: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="2126d-281">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="2126d-282">На портале Azure параметры приложения сохраняются в два шага: сохраните параметр `WEBSITE_LOAD_CERTIFICATES`, а затем в верхней части колонки нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="2126d-282">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="2126d-283">Выберите **параметры TLS/SSL** приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-283">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="2126d-284">Выберите **Сертификаты закрытых ключей (PFX)** .</span><span class="sxs-lookup"><span data-stu-id="2126d-284">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="2126d-285">Дважды выполните процесс **Импорт сертификата Key Vault**, чтобы импортировать и сертификат сайта для обмена данными по протоколу HTTPS, и самозаверяющий сертификат сайта для подписывания токена Identity Server.</span><span class="sxs-lookup"><span data-stu-id="2126d-285">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="2126d-286">Перейдите к колонке **Личные домены**.</span><span class="sxs-lookup"><span data-stu-id="2126d-286">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="2126d-287">На веб-сайте регистратора своего домена используйте **IP-адрес** и **идентификатор проверки личного домена** для настройки домена.</span><span class="sxs-lookup"><span data-stu-id="2126d-287">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="2126d-288">Типичная конфигурация домена включает в себя следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="2126d-288">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="2126d-289">**запись A** с **узлом** `@` и значением IP-адреса с портала Azure;</span><span class="sxs-lookup"><span data-stu-id="2126d-289">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="2126d-290">**запись TXT** с **узлом** `asuid` и значением идентификатора проверки, созданным Azure и предоставленным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="2126d-290">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="2126d-291">Надлежащим образом сохраните изменения на веб-сайте регистратора домена.</span><span class="sxs-lookup"><span data-stu-id="2126d-291">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="2126d-292">На веб-сайтах некоторых регистраторов записи домена сохраняются в два шага: сначала нужно по отдельности сохранить одну запись или несколько, а затем обновить регистрацию домена с помощью специальной кнопки.</span><span class="sxs-lookup"><span data-stu-id="2126d-292">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="2126d-293">Вернитесь к колонке **Личные домены** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="2126d-293">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="2126d-294">Нажмите кнопку **Добавить личный домен**.</span><span class="sxs-lookup"><span data-stu-id="2126d-294">Select **Add custom domain**.</span></span> <span data-ttu-id="2126d-295">Выберите параметр **Запись A**.</span><span class="sxs-lookup"><span data-stu-id="2126d-295">Select the **A Record** option.</span></span> <span data-ttu-id="2126d-296">Укажите домен и выберите команду **Проверить**.</span><span class="sxs-lookup"><span data-stu-id="2126d-296">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="2126d-297">Если записи домена верны и распространены в Интернете, на портале можно будет нажать кнопку **Добавить личный домен**.</span><span class="sxs-lookup"><span data-stu-id="2126d-297">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="2126d-298">На распространение изменений регистрации домена на серверах доменных имен (DNS) в Интернете после их обработки регистратором домена может потребоваться несколько дней.</span><span class="sxs-lookup"><span data-stu-id="2126d-298">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="2126d-299">Если записи домена не были обновлены в течение трех рабочих дней, убедитесь в том, что они были правильно заданы на сайте регистратора, и обратитесь в его службу поддержки клиентов.</span><span class="sxs-lookup"><span data-stu-id="2126d-299">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="2126d-300">В колонке **Личные домены** **СОСТОЯНИЕ SSL** домена должно иметь значение `Not Secure`.</span><span class="sxs-lookup"><span data-stu-id="2126d-300">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="2126d-301">Щелкните ссылку **Добавить привязку**.</span><span class="sxs-lookup"><span data-stu-id="2126d-301">Select the **Add binding** link.</span></span> <span data-ttu-id="2126d-302">Выберите HTTPS-сертификат сайта из хранилища ключей для привязки личного домена.</span><span class="sxs-lookup"><span data-stu-id="2126d-302">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="2126d-303">В Visual Studio откройте файл параметров приложения для *серверного* проекта (`appsettings.json` или `appsettings.Production.json`).</span><span class="sxs-lookup"><span data-stu-id="2126d-303">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="2126d-304">В конфигурации Identity Server добавьте приведенный ниже раздел `Key`.</span><span class="sxs-lookup"><span data-stu-id="2126d-304">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="2126d-305">Укажите **субъект** самозаверяющего сертификата для ключа `Name`.</span><span class="sxs-lookup"><span data-stu-id="2126d-305">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="2126d-306">В следующем примере в хранилище ключей сертификату присвоено общее имя `IdentityServerSigning`, поэтому **субъект** — `CN=IdentityServerSigning`.</span><span class="sxs-lookup"><span data-stu-id="2126d-306">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="2126d-307">В Visual Studio создайте [профиль публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Службы приложений Azure для *серверного* проекта.</span><span class="sxs-lookup"><span data-stu-id="2126d-307">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="2126d-308">В строке меню выберите **Сборка** > **Опубликовать** > **Создать** > **Azure** > **Служба приложений Azure** (Windows или Linux).</span><span class="sxs-lookup"><span data-stu-id="2126d-308">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="2126d-309">Когда Visual Studio подключится к подписке Azure, вы сможете настроить **представление** ресурсов Azure по **типу ресурса**.</span><span class="sxs-lookup"><span data-stu-id="2126d-309">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="2126d-310">В списке **Веб-приложение** найдите Службу приложений для приложения и выберите ее.</span><span class="sxs-lookup"><span data-stu-id="2126d-310">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="2126d-311">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="2126d-311">Select **Finish**.</span></span>
1. <span data-ttu-id="2126d-312">Когда в Visual Studio снова отобразится окно **Публикация**, зависимости хранилища ключей и службы базы данных SQL Server будут обнаружены автоматически.</span><span class="sxs-lookup"><span data-stu-id="2126d-312">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="2126d-313">Для службы хранилища ключей не требуется изменять настройки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2126d-313">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="2126d-314">В целях тестирования с приложением можно развернуть локальную базу данных [SQLite](https://www.sqlite.org/index.html), которая настраивается по умолчанию на основе шаблона Blazor. Дополнительной настройки она не требует.</span><span class="sxs-lookup"><span data-stu-id="2126d-314">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="2126d-315">Настройка другой базы данных для Identity Server в рабочей среде выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="2126d-315">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="2126d-316">Дополнительные сведения см. в ресурсах, посвященных базам данных, в следующих наборах документации.</span><span class="sxs-lookup"><span data-stu-id="2126d-316">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="2126d-317">Служба приложений</span><span class="sxs-lookup"><span data-stu-id="2126d-317">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="2126d-318">Сервер Identity</span><span class="sxs-lookup"><span data-stu-id="2126d-318">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="2126d-319">Щелкните ссылку **Изменить** под именем профиля развертывания в верхней части окна.</span><span class="sxs-lookup"><span data-stu-id="2126d-319">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="2126d-320">Измените URL-адрес назначения на URL-адрес личного домена сайта (например, `https://www.contoso.com`).</span><span class="sxs-lookup"><span data-stu-id="2126d-320">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="2126d-321">Сохраните параметры.</span><span class="sxs-lookup"><span data-stu-id="2126d-321">Save the settings.</span></span>
1. <span data-ttu-id="2126d-322">Опубликуйте приложение.</span><span class="sxs-lookup"><span data-stu-id="2126d-322">Publish the app.</span></span> <span data-ttu-id="2126d-323">В Visual Studio откроется окно браузера, и будет выполнен запрос к сайту в личном домене.</span><span class="sxs-lookup"><span data-stu-id="2126d-323">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="2126d-324">В документации по Azure приводятся дополнительные сведения об использовании служб Azure и личных доменов с привязкой TLS в Службе приложений, включая сведения об использовании записей CNAME вместо записей A.</span><span class="sxs-lookup"><span data-stu-id="2126d-324">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="2126d-325">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="2126d-325">For more information, see the following resources:</span></span>

* [<span data-ttu-id="2126d-326">Документация по Службе приложений</span><span class="sxs-lookup"><span data-stu-id="2126d-326">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="2126d-327">Руководство по Сопоставление существующего настраиваемого DNS-имени со Службой приложений Azure</span><span class="sxs-lookup"><span data-stu-id="2126d-327">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="2126d-328">Защита пользовательского доменного имени с помощью привязки TLS/SSL в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="2126d-328">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="2126d-329">Хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="2126d-329">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="2126d-330">Мы рекомендуем использовать новое окно браузера в режиме InPrivate или инкогнито для каждого тестового запуска после изменения приложения, его конфигурации или служб Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="2126d-330">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="2126d-331">Оставшиеся после предыдущего тестового запуска файлы cookie могут привести к ошибкам проверки подлинности или авторизации при тестировании сайта, даже если его конфигурация верна.</span><span class="sxs-lookup"><span data-stu-id="2126d-331">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="2126d-332">Дополнительные сведения о том, как настроить Visual Studio для открытия нового окна браузера в режиме InPrivate или инкогнито для каждого тестового запуска, см. в разделе [Файлы Cookie и данные сайта](#cookies-and-site-data).</span><span class="sxs-lookup"><span data-stu-id="2126d-332">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="2126d-333">При изменении конфигурации Службы приложений на портале Azure обновления обычно вступают в силу быстро, но не мгновенно.</span><span class="sxs-lookup"><span data-stu-id="2126d-333">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="2126d-334">Иногда, чтобы изменение конфигурации вступило в силу, необходимо немного подождать, пока Служба приложений перезапустится.</span><span class="sxs-lookup"><span data-stu-id="2126d-334">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="2126d-335">При устранении проблемы с загрузкой сертификата выполните приведенную ниже команду в командной оболочке PowerShell [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="2126d-335">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="2126d-336">Она выводит список сертификатов, к которым приложение может получить доступ в хранилище сертификатов `My` > `CurrentUser`.</span><span class="sxs-lookup"><span data-stu-id="2126d-336">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="2126d-337">Выходные данные включают в себя субъекты и отпечатки сертификатов, которые могут быть полезны при отладке приложения.</span><span class="sxs-lookup"><span data-stu-id="2126d-337">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2126d-338">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2126d-338">Additional resources</span></span>

* [<span data-ttu-id="2126d-339">Развертывание в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="2126d-339">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="2126d-340">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="2126d-340">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2126d-341">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2126d-341">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="2126d-342"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="2126d-342"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="2126d-343">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="2126d-343">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="2126d-344">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="2126d-344">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
