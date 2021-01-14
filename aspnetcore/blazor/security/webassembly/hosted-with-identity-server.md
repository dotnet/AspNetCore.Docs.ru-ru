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
ms.openlocfilehash: fdd7eb3c4a3b07022760a43cbde80838bfaf7c84
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024799"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="9fcc5-103">Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="9fcc5-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="9fcc5-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="9fcc5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9fcc5-105">В этой статье объясняется, как создать новое [размещенное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует [IdentityServer](https://identityserver.io/) для аутентификации пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="9fcc5-106">Чтобы настроить изолированное или размещенное приложение Blazor WebAssembly для использования существующего внешнего экземпляра Identity Server, следуйте рекомендациям в статье <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9fcc5-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fcc5-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9fcc5-108">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="9fcc5-109">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="9fcc5-110">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="9fcc5-111">Установите флажок **С размещением в ASP.NET Core** в разделе **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="9fcc5-112">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fcc5-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="9fcc5-113">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке, укажите механизм аутентификации `Individual` с параметром `-au|--auth` для хранения пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="9fcc5-114">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="9fcc5-114">Placeholder</span></span>  | <span data-ttu-id="9fcc5-115">Пример</span><span class="sxs-lookup"><span data-stu-id="9fcc5-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="9fcc5-116">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="9fcc5-117">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9fcc5-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="9fcc5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9fcc5-119">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="9fcc5-120">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="9fcc5-121">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="9fcc5-122">Установите флажок **С размещением в ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="9fcc5-123">Конфигурация приложения *`Server`*</span><span class="sxs-lookup"><span data-stu-id="9fcc5-123">*`Server`* app configuration</span></span>

<span data-ttu-id="9fcc5-124">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="9fcc5-125">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="9fcc5-125">Startup class</span></span>

<span data-ttu-id="9fcc5-126">В классе `Startup` представлены следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="9fcc5-127">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="9fcc5-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="9fcc5-129">IdentityServer с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="9fcc5-130">Аутентификация с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="9fcc5-131">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="9fcc5-132">ПО промежуточного слоя IdentityServer предоставляет конечные точки OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="9fcc5-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="9fcc5-133">ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="9fcc5-134">ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="9fcc5-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="9fcc5-135">AddApiAuthorization</span></span>

<span data-ttu-id="9fcc5-136">Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="9fcc5-137">IdentityServer — это функциональная и расширяемая платформа для повышения уровня безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="9fcc5-138">IdentityServer указывает на ненужную сложность в некоторых распространенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="9fcc5-139">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="9fcc5-140">Если нужно изменить требования к аутентификации, можно воспользоваться широкими возможностями IdentityServer для настройки аутентификации в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="9fcc5-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="9fcc5-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="9fcc5-142">Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="9fcc5-143">Политика настроена так, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов Identity `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="9fcc5-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="9fcc5-145">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-145">Additionally, this method:</span></span>

* <span data-ttu-id="9fcc5-146">Регистрирует ресурс API `{APPLICATION NAME}API` в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="9fcc5-147">Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="9fcc5-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="9fcc5-148">WeatherForecastController</span></span>

<span data-ttu-id="9fcc5-149">В `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) к классу применяется атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="9fcc5-150">Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="9fcc5-151">Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="9fcc5-152">Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="9fcc5-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="9fcc5-153">ApplicationDbContext</span></span>

<span data-ttu-id="9fcc5-154">В `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="9fcc5-155">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="9fcc5-156">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов Identity <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы Identity путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="9fcc5-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="9fcc5-157">OidcConfigurationController</span></span>

<span data-ttu-id="9fcc5-158">В `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) выполняется подготовка конечной точки клиента для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="9fcc5-159">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="9fcc5-159">App settings</span></span>

<span data-ttu-id="9fcc5-160">В файле параметров приложения (`appsettings.json`) в корневом каталоге проекта в разделе `IdentityServer` описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="9fcc5-161">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-161">In the following example, there's a single client.</span></span> <span data-ttu-id="9fcc5-162">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="9fcc5-163">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="9fcc5-164">Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="9fcc5-165">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="9fcc5-166">Конфигурация приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="9fcc5-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9fcc5-167">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9fcc5-167">Authentication package</span></span>

<span data-ttu-id="9fcc5-168">Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="9fcc5-169">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9fcc5-170">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="9fcc5-171">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="9fcc5-172">Конфигурация `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="9fcc5-172">`HttpClient` configuration</span></span>

<span data-ttu-id="9fcc5-173">В `Program.Main` (`Program.cs`) именованный <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) настроен для предоставления экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при отправке запросов к API сервера:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="9fcc5-174">Если вы хотите настроить приложение Blazor WebAssembly на использование существующего экземпляра IdentityServer, который не является частью размещенного решения Blazor, измените регистрацию базового адреса <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) на URL-адрес конечной точки авторизации API серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="9fcc5-175">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="9fcc5-175">API authorization support</span></span>

<span data-ttu-id="9fcc5-176">Поддержка проверки подлинности пользователей подключается в контейнере службы с помощью метода расширения, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="9fcc5-177">Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="9fcc5-178">По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="9fcc5-179">По соглашению в качестве идентификатора клиента используется имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="9fcc5-180">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="9fcc5-181">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="9fcc5-181">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9fcc5-182">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="9fcc5-182">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="9fcc5-183">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="9fcc5-183">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9fcc5-184">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9fcc5-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9fcc5-185">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9fcc5-185">LoginDisplay component</span></span>

<span data-ttu-id="9fcc5-186">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="9fcc5-187">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-187">For authenticated users:</span></span>
  * <span data-ttu-id="9fcc5-188">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-188">Displays the current user name.</span></span>
  * <span data-ttu-id="9fcc5-189">Отображает ссылку на страницу профиля пользователя в ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="9fcc5-190">Отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="9fcc5-191">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-191">For anonymous users:</span></span>
  * <span data-ttu-id="9fcc5-192">Предоставляет возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-192">Offers the option to register.</span></span>
  * <span data-ttu-id="9fcc5-193">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-193">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="9fcc5-194">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="9fcc5-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9fcc5-195">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="9fcc5-195">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9fcc5-196">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="9fcc5-196">Run the app</span></span>

<span data-ttu-id="9fcc5-197">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-197">Run the app from the Server project.</span></span> <span data-ttu-id="9fcc5-198">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="9fcc5-199">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="9fcc5-200">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="9fcc5-201">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="9fcc5-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="9fcc5-202">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="9fcc5-202">Custom user factory</span></span>

<span data-ttu-id="9fcc5-203">В приложении *`Client`* создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="9fcc5-204">Identity Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="9fcc5-205">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="9fcc5-206">Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="9fcc5-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-207">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="9fcc5-208">В приложении *`Client`* зарегистрируйте фабрику в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="9fcc5-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="9fcc5-209">В приложении *`Server`* вызовите <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в построителе Identity, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="9fcc5-210">Настройка Identity Server</span><span class="sxs-lookup"><span data-stu-id="9fcc5-210">Configure Identity Server</span></span>

<span data-ttu-id="9fcc5-211">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="9fcc5-212">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="9fcc5-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="9fcc5-213">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="9fcc5-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="9fcc5-214">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="9fcc5-214">API authorization options</span></span>

<span data-ttu-id="9fcc5-215">В приложении *`Server`* :</span><span class="sxs-lookup"><span data-stu-id="9fcc5-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="9fcc5-216">Настройте Identity Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="9fcc5-217">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="9fcc5-218">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="9fcc5-218">Profile Service</span></span>

<span data-ttu-id="9fcc5-219">В приложении *`Server`* создайте реализацию `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="9fcc5-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-220">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="9fcc5-221">В приложении *`Server`* зарегистрируйте службу профиля в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="9fcc5-222">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="9fcc5-222">Use authorization mechanisms</span></span>

<span data-ttu-id="9fcc5-223">На этом этапе подходы с авторизацией компонентов в приложении *`Client`* являются функциональными.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="9fcc5-224">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="9fcc5-225">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="9fcc5-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="9fcc5-226">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="9fcc5-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="9fcc5-227">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="9fcc5-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="9fcc5-228">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="9fcc5-229">Для `User.Identity.Name` в приложении *`Client`* указывается имя пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-229">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="9fcc5-230">Размещение в Службе приложений Azure с использованием личного домена</span><span class="sxs-lookup"><span data-stu-id="9fcc5-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="9fcc5-231">Ниже объясняется, как развернуть размещенное приложение Blazor WebAssembly с Identity Server в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) с использованием личного домена.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="9fcc5-232">В этом сценарии размещения **не** используйте один и тот же сертификат для [ключа подписывания токена Identity Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) и защищенного обмена данными по протоколу HTTPS между сайтом и браузерами.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="9fcc5-233">Использование разных сертификатов в этих целях рекомендуется из соображений безопасности, так как позволяет изолировать закрытые ключи разного назначения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="9fcc5-234">Управление TLS-сертификатами для обмена данными с браузерами производится независимо и не влияет на подписывание маркеров Identity Server.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="9fcc5-235">Когда [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) предоставляет сертификат приложению Службы приложений для привязки к личному домену, Identity Server не может получить тот же сертификат из Azure Key Vault для подписывания токена.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="9fcc5-236">Хотя Identity Server можно настроить так, чтобы он использовал тот же TLS-сертификат по физическому пути, размещать сертификаты безопасности в системе управления версиями **не рекомендуется, в большинстве случаев этого следует избегать**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="9fcc5-237">Далее в Azure Key Vault создается самозаверяющий сертификат исключительно для подписывания токена Identity Server.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="9fcc5-238">Конфигурация Identity Server использует сертификат из хранилища ключей посредством хранилища сертификатов `My` > `CurrentUser` приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="9fcc5-239">Другие сертификаты, используемые для трафика HTTPS личных доменов, создаются и настраиваются отдельно от сертификата подписывания Identity Server.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="9fcc5-240">Чтобы настроить приложение, Службу приложений Azure и Azure Key Vault для размещения в личном домене с HTTPS, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="9fcc5-241">Создайте [план Службы приложений](/azure/app-service/overview-hosting-plans) уровня `Basic B1` или выше.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="9fcc5-242">Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="9fcc5-243">Создайте PFX-сертификат для безопасного обмена данными между сайтом и браузером (по протоколу HTTPS) с полным доменным именем (FQDN) сайта, которое контролирует организация (например, `www.contoso.com`), в качестве общего имени.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="9fcc5-244">Создайте сертификат с указанными ниже параметрами.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-244">Create the certificate with:</span></span>
   * <span data-ttu-id="9fcc5-245">Ключ использует:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-245">Key uses</span></span>
     * <span data-ttu-id="9fcc5-246">проверку цифровой подписи (`digitalSignature`);</span><span class="sxs-lookup"><span data-stu-id="9fcc5-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="9fcc5-247">шифрование ключей (`keyEncipherment`).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="9fcc5-248">Расширенное использование ключа:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="9fcc5-249">проверка подлинности клиента (1.3.6.1.5.5.7.3.2);</span><span class="sxs-lookup"><span data-stu-id="9fcc5-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="9fcc5-250">проверка подлинности сервера (1.3.6.1.5.5.7.3.1).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="9fcc5-251">Чтобы создать сертификат, используйте один из следующих подходов или любое другое подходящее средство или веб-службу.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="9fcc5-252">Хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="9fcc5-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="9fcc5-253">MakeCert в Windows</span><span class="sxs-lookup"><span data-stu-id="9fcc5-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="9fcc5-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="9fcc5-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="9fcc5-255">Запишите пароль, который будет использоваться позже для импорта сертификата в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="9fcc5-256">Дополнительные сведения о сертификатах Azure Key Vault см. в статье [Azure Key Vault. Сертификаты](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="9fcc5-257">Создайте хранилище Azure Key Vault или используйте уже имеющееся в подписке Azure.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="9fcc5-258">В области **Сертификаты** хранилища ключей импортируйте сертификат сайта PFX.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="9fcc5-259">Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="9fcc5-260">В Azure Key Vault создайте самозаверяющий сертификат для подписывания токена Identity Server.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="9fcc5-261">Укажите **имя сертификата** и **субъект**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="9fcc5-262">**Субъект** указывается в формате `CN={COMMON NAME}`, где заполнитель `{COMMON NAME}` — это общее имя сертификата.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="9fcc5-263">Общим именем может быть любая буквенно-цифровая строка.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="9fcc5-264">Например, `CN=IdentityServerSigning` — допустимый **субъект** сертификата.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="9fcc5-265">В разделе **Расширенная настройка политик** оставьте значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="9fcc5-266">Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="9fcc5-267">Перейдите к Службе приложений Azure на портале Azure и создайте Службу приложений со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="9fcc5-268">**Опубликовать** — `Code`;</span><span class="sxs-lookup"><span data-stu-id="9fcc5-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="9fcc5-269">**Стек времени выполнения** — среда выполнения приложения;</span><span class="sxs-lookup"><span data-stu-id="9fcc5-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="9fcc5-270">**Номер SKU и размер** — уровень Службы приложений не ниже `Basic B1`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="9fcc5-271">Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="9fcc5-272">После того как в Azure будет создана Служба приложений, откройте **конфигурацию приложения** и добавьте новый параметр, указав записанные ранее отпечатки сертификатов.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="9fcc5-273">Ключ параметра — `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="9fcc5-274">В значении параметра разделяйте отпечатки запятыми, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="9fcc5-275">Ключ: `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="9fcc5-276">Значение: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="9fcc5-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="9fcc5-277">На портале Azure параметры приложения сохраняются в два шага: сохраните параметр `WEBSITE_LOAD_CERTIFICATES`, а затем в верхней части колонки нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="9fcc5-278">Выберите **параметры TLS/SSL** приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="9fcc5-279">Выберите **Сертификаты закрытых ключей (PFX)** .</span><span class="sxs-lookup"><span data-stu-id="9fcc5-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="9fcc5-280">Дважды выполните процесс **Импорт сертификата Key Vault**, чтобы импортировать и сертификат сайта для обмена данными по протоколу HTTPS, и самозаверяющий сертификат сайта для подписывания токена Identity Server.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="9fcc5-281">Перейдите к колонке **Личные домены**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="9fcc5-282">На веб-сайте регистратора своего домена используйте **IP-адрес** и **идентификатор проверки личного домена** для настройки домена.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="9fcc5-283">Типичная конфигурация домена включает в себя следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="9fcc5-284">**запись A** с **узлом** `@` и значением IP-адреса с портала Azure;</span><span class="sxs-lookup"><span data-stu-id="9fcc5-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="9fcc5-285">**запись TXT** с **узлом** `asuid` и значением идентификатора проверки, созданным Azure и предоставленным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="9fcc5-286">Надлежащим образом сохраните изменения на веб-сайте регистратора домена.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="9fcc5-287">На веб-сайтах некоторых регистраторов записи домена сохраняются в два шага: сначала нужно по отдельности сохранить одну запись или несколько, а затем обновить регистрацию домена с помощью специальной кнопки.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="9fcc5-288">Вернитесь к колонке **Личные домены** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="9fcc5-289">Нажмите кнопку **Добавить личный домен**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-289">Select **Add custom domain**.</span></span> <span data-ttu-id="9fcc5-290">Выберите параметр **Запись A**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-290">Select the **A Record** option.</span></span> <span data-ttu-id="9fcc5-291">Укажите домен и выберите команду **Проверить**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="9fcc5-292">Если записи домена верны и распространены в Интернете, на портале можно будет нажать кнопку **Добавить личный домен**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="9fcc5-293">На распространение изменений регистрации домена на серверах доменных имен (DNS) в Интернете после их обработки регистратором домена может потребоваться несколько дней.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="9fcc5-294">Если записи домена не были обновлены в течение трех рабочих дней, убедитесь в том, что они были правильно заданы на сайте регистратора, и обратитесь в его службу поддержки клиентов.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="9fcc5-295">В колонке **Личные домены** **СОСТОЯНИЕ SSL** домена должно иметь значение `Not Secure`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="9fcc5-296">Щелкните ссылку **Добавить привязку**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-296">Select the **Add binding** link.</span></span> <span data-ttu-id="9fcc5-297">Выберите HTTPS-сертификат сайта из хранилища ключей для привязки личного домена.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="9fcc5-298">В Visual Studio откройте файл параметров приложения для *серверного* проекта (`appsettings.json` или `appsettings.Production.json`).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="9fcc5-299">В конфигурации Identity Server добавьте приведенный ниже раздел `Key`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="9fcc5-300">Укажите **субъект** самозаверяющего сертификата для ключа `Name`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="9fcc5-301">В следующем примере в хранилище ключей сертификату присвоено общее имя `IdentityServerSigning`, поэтому **субъект** — `CN=IdentityServerSigning`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

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

1. <span data-ttu-id="9fcc5-302">В Visual Studio создайте [профиль публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Службы приложений Azure для *серверного* проекта.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="9fcc5-303">В строке меню выберите **Сборка** > **Опубликовать** > **Создать** > **Azure** > **Служба приложений Azure** (Windows или Linux).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="9fcc5-304">Когда Visual Studio подключится к подписке Azure, вы сможете настроить **представление** ресурсов Azure по **типу ресурса**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="9fcc5-305">В списке **Веб-приложение** найдите Службу приложений для приложения и выберите ее.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="9fcc5-306">Нажмите кнопку **Готово**.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-306">Select **Finish**.</span></span>
1. <span data-ttu-id="9fcc5-307">Когда в Visual Studio снова отобразится окно **Публикация**, зависимости хранилища ключей и службы базы данных SQL Server будут обнаружены автоматически.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="9fcc5-308">Для службы хранилища ключей не требуется изменять настройки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="9fcc5-309">В целях тестирования с приложением можно развернуть локальную базу данных [SQLite](https://www.sqlite.org/index.html), которая настраивается по умолчанию на основе шаблона Blazor. Дополнительной настройки она не требует.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="9fcc5-310">Настройка другой базы данных для Identity Server в рабочей среде выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="9fcc5-311">Дополнительные сведения см. в ресурсах, посвященных базам данных, в следующих наборах документации.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="9fcc5-312">Служба приложений</span><span class="sxs-lookup"><span data-stu-id="9fcc5-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="9fcc5-313">Сервер Identity</span><span class="sxs-lookup"><span data-stu-id="9fcc5-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="9fcc5-314">Щелкните ссылку **Изменить** под именем профиля развертывания в верхней части окна.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="9fcc5-315">Измените URL-адрес назначения на URL-адрес личного домена сайта (например, `https://www.contoso.com`).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="9fcc5-316">Сохраните параметры.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-316">Save the settings.</span></span>
1. <span data-ttu-id="9fcc5-317">Опубликуйте приложение.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-317">Publish the app.</span></span> <span data-ttu-id="9fcc5-318">В Visual Studio откроется окно браузера, и будет выполнен запрос к сайту в личном домене.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="9fcc5-319">В документации по Azure приводятся дополнительные сведения об использовании служб Azure и личных доменов с привязкой TLS в Службе приложений, включая сведения об использовании записей CNAME вместо записей A.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="9fcc5-320">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="9fcc5-321">Документация по Службе приложений</span><span class="sxs-lookup"><span data-stu-id="9fcc5-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="9fcc5-322">Руководство по Сопоставление существующего настраиваемого DNS-имени со Службой приложений Azure</span><span class="sxs-lookup"><span data-stu-id="9fcc5-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="9fcc5-323">Защита пользовательского доменного имени с помощью привязки TLS/SSL в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="9fcc5-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="9fcc5-324">Хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="9fcc5-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="9fcc5-325">Мы рекомендуем использовать новое окно браузера в режиме InPrivate или инкогнито для каждого тестового запуска после изменения приложения, его конфигурации или служб Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="9fcc5-326">Оставшиеся после предыдущего тестового запуска файлы cookie могут привести к ошибкам проверки подлинности или авторизации при тестировании сайта, даже если его конфигурация верна.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="9fcc5-327">Дополнительные сведения о том, как настроить Visual Studio для открытия нового окна браузера в режиме InPrivate или инкогнито для каждого тестового запуска, см. в разделе [Файлы Cookie и данные сайта](#cookies-and-site-data).</span><span class="sxs-lookup"><span data-stu-id="9fcc5-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="9fcc5-328">При изменении конфигурации Службы приложений на портале Azure обновления обычно вступают в силу быстро, но не мгновенно.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="9fcc5-329">Иногда, чтобы изменение конфигурации вступило в силу, необходимо немного подождать, пока Служба приложений перезапустится.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="9fcc5-330">При устранении проблемы с загрузкой сертификата выполните приведенную ниже команду в командной оболочке PowerShell [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="9fcc5-331">Она выводит список сертификатов, к которым приложение может получить доступ в хранилище сертификатов `My` > `CurrentUser`.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="9fcc5-332">Выходные данные включают в себя субъекты и отпечатки сертификатов, которые могут быть полезны при отладке приложения.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9fcc5-333">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9fcc5-333">Additional resources</span></span>

* [<span data-ttu-id="9fcc5-334">Развертывание в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="9fcc5-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="9fcc5-335">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="9fcc5-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="9fcc5-336">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9fcc5-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="9fcc5-337"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="9fcc5-337"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="9fcc5-338">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-338">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="9fcc5-339">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="9fcc5-339">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
