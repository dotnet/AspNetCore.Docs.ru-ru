---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности.
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 3da9ea045de996602ead052f6f13ffc999273a50
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252491"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="b07f0-103">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b07f0-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="b07f0-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="b07f0-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b07f0-105">*При использовании Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этой статье. См. статьи, посвященные AAD и AAD B2C, в этом разделе оглавления.*</span><span class="sxs-lookup"><span data-stu-id="b07f0-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="b07f0-106">Чтобы создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует библиотеку [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), выполните рекомендации с учетом используемых средств.</span><span class="sxs-lookup"><span data-stu-id="b07f0-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

> [!NOTE]
> <span data-ttu-id="b07f0-107">Поставщик Identity (IP) должен использовать [OpenID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="b07f0-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="b07f0-108">Например, поставщик IP Facebook несовместим с OIDC, поэтому к нему не применяются рекомендации, приведенные в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="b07f0-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="b07f0-109">Для получения дополнительной информации см. <xref:blazor/security/webassembly/index#authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="b07f0-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b07f0-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b07f0-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b07f0-111">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b07f0-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="b07f0-112">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="b07f0-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="b07f0-113">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b07f0-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="b07f0-114">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b07f0-114">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="b07f0-115">Создайте новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке.</span><span class="sxs-lookup"><span data-stu-id="b07f0-115">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="b07f0-116">Задайте механизм аутентификации `Individual` с параметром `-au|--auth`, чтобы сохранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b07f0-116">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="b07f0-117">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="b07f0-117">Placeholder</span></span>  | <span data-ttu-id="b07f0-118">Пример</span><span class="sxs-lookup"><span data-stu-id="b07f0-118">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="b07f0-119">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="b07f0-119">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="b07f0-120">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b07f0-120">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b07f0-121">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="b07f0-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="b07f0-122">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="b07f0-122">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="b07f0-123">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="b07f0-123">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b07f0-124">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b07f0-124">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="b07f0-125">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b07f0-125">Authentication package</span></span>

<span data-ttu-id="b07f0-126">Когда приложение создается для использования отдельных учетных записей пользователей, оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="b07f0-126">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="b07f0-127">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="b07f0-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b07f0-128">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="b07f0-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="b07f0-129">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="b07f0-129">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b07f0-130">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b07f0-130">Authentication service support</span></span>

<span data-ttu-id="b07f0-131">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="b07f0-131">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="b07f0-132">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="b07f0-132">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b07f0-133">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b07f0-133">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="b07f0-134">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b07f0-134">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="b07f0-135">Пример OIDC Google OAuth 2.0:</span><span class="sxs-lookup"><span data-stu-id="b07f0-135">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="b07f0-136">URI перенаправления (`https://localhost:5001/authentication/login-callback`) регистрируется в [консоли API Google](https://console.developers.google.com/apis/dashboard) в разделе **Учетные данные** >  **`{NAME}`**  > **Разрешенные URI перенаправления**, где `{NAME}` — это имя клиента приложения в списке **идентификаторов клиентов OAuth 2.0** приложения в консоли Google API.</span><span class="sxs-lookup"><span data-stu-id="b07f0-136">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="b07f0-137">Поддержка проверки подлинности для изолированных приложений обеспечивается посредством OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="b07f0-137">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="b07f0-138">Метод <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="b07f0-138">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="b07f0-139">Значения, необходимые для настройки приложения, можно получить от поставщика удостоверений, совместимого с OIDC.</span><span class="sxs-lookup"><span data-stu-id="b07f0-139">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="b07f0-140">Получите эти значения при регистрации приложения, которая обычно производится на веб-портале.</span><span class="sxs-lookup"><span data-stu-id="b07f0-140">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="b07f0-141">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="b07f0-141">Access token scopes</span></span>

<span data-ttu-id="b07f0-142">Шаблон Blazor WebAssembly автоматически настраивает области по умолчанию для `openid` и `profile`.</span><span class="sxs-lookup"><span data-stu-id="b07f0-142">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="b07f0-143">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="b07f0-143">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b07f0-144">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера по умолчанию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="b07f0-144">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="b07f0-145">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="b07f0-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b07f0-146">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="b07f0-146">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b07f0-147">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="b07f0-147">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b07f0-148">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="b07f0-148">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b07f0-149">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="b07f0-149">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="b07f0-150">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="b07f0-150">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b07f0-151">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="b07f0-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b07f0-152">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="b07f0-152">LoginDisplay component</span></span>

<span data-ttu-id="b07f0-153">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="b07f0-153">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="b07f0-154">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="b07f0-154">For authenticated users:</span></span>
  * <span data-ttu-id="b07f0-155">отображает имя текущего пользователя;</span><span class="sxs-lookup"><span data-stu-id="b07f0-155">Displays the current username.</span></span>
  * <span data-ttu-id="b07f0-156">отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="b07f0-156">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="b07f0-157">Для анонимных пользователей предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="b07f0-157">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a><span data-ttu-id="b07f0-158">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b07f0-158">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b07f0-159">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b07f0-159">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="b07f0-160">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="b07f0-160">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="b07f0-161"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="b07f0-161"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="b07f0-162">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="b07f0-162">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="b07f0-163">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="b07f0-163">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
