---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280883"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="58612-103">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="58612-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="58612-104">*При использовании Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этой статье. См. статьи, посвященные AAD и AAD B2C, в этом разделе оглавления.*</span><span class="sxs-lookup"><span data-stu-id="58612-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="58612-105">Чтобы создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует библиотеку [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), выполните рекомендации с учетом используемых средств.</span><span class="sxs-lookup"><span data-stu-id="58612-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span> <span data-ttu-id="58612-106">При добавлении поддержки проверки подлинности следуйте рекомендациям по настройке и конфигурированию приложения в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="58612-106">If adding support for authentication, see the following sections of this article for guidance on setting up and configuring the app.</span></span>

> [!NOTE]
> <span data-ttu-id="58612-107">Поставщик Identity (IP) должен использовать [OpenID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="58612-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="58612-108">Например, поставщик IP Facebook несовместим с OIDC, поэтому к нему не применяются рекомендации, приведенные в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="58612-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="58612-109">Для получения дополнительной информации см. <xref:blazor/security/webassembly/index#authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="58612-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58612-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58612-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58612-111">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="58612-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="58612-112">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="58612-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="58612-113">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы использовать систему [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58612-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="58612-114">Это позволяет добавить поддержку проверки подлинности и не приводит к хранению пользователей в базе данных.</span><span class="sxs-lookup"><span data-stu-id="58612-114">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="58612-115">В следующих разделах этой статьи приведены дополнительные сведения.</span><span class="sxs-lookup"><span data-stu-id="58612-115">The following sections of this article provide further details.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="58612-116">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="58612-116">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="58612-117">Создайте новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке.</span><span class="sxs-lookup"><span data-stu-id="58612-117">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="58612-118">Задайте механизм аутентификации `Individual` с параметром `-au|--auth`, чтобы использовать систему [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58612-118">Specify the `Individual` authentication mechanism with the `-au|--auth` option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="58612-119">Это позволяет добавить поддержку проверки подлинности и не приводит к хранению пользователей в базе данных.</span><span class="sxs-lookup"><span data-stu-id="58612-119">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="58612-120">В следующих разделах этой статьи приведены дополнительные сведения.</span><span class="sxs-lookup"><span data-stu-id="58612-120">The following sections of this article provide further details.</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="58612-121">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="58612-121">Placeholder</span></span>  | <span data-ttu-id="58612-122">Пример</span><span class="sxs-lookup"><span data-stu-id="58612-122">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="58612-123">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="58612-123">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="58612-124">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="58612-124">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58612-125">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="58612-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58612-126">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="58612-126">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="58612-127">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="58612-127">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="58612-128">Приложение создается для использования [Identity](xref:security/authentication/identity) ASP.NET Core и не приводит к хранению пользователей в базе данных.</span><span class="sxs-lookup"><span data-stu-id="58612-128">The app is created to use ASP.NET Core [Identity](xref:security/authentication/identity) and doesn't result in storing users in a database.</span></span> <span data-ttu-id="58612-129">В следующих разделах этой статьи приведены дополнительные сведения.</span><span class="sxs-lookup"><span data-stu-id="58612-129">The following sections of this article provide further details.</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="58612-130">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="58612-130">Authentication package</span></span>

<span data-ttu-id="58612-131">Когда приложение создается для использования отдельных учетных записей пользователей, оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="58612-131">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="58612-132">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="58612-132">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="58612-133">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="58612-133">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="58612-134">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="58612-134">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="58612-135">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="58612-135">Authentication service support</span></span>

<span data-ttu-id="58612-136">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="58612-136">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="58612-137">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="58612-137">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="58612-138">Для нового приложения укажите значения для заполнителей `{AUTHORITY}` и `{CLIENT ID}` в следующей конфигурации.</span><span class="sxs-lookup"><span data-stu-id="58612-138">For a new app, provide values for the `{AUTHORITY}` and `{CLIENT ID}` placeholders in the following configuration.</span></span> <span data-ttu-id="58612-139">Укажите другие значения конфигурации, необходимые для использования с IP-адресом приложения.</span><span class="sxs-lookup"><span data-stu-id="58612-139">Provide other configuration values that are required for use with the app's IP.</span></span> <span data-ttu-id="58612-140">В качестве примера используется Google, для чего требуются `PostLogoutRedirectUri`, `RedirectUri` и `ResponseType`.</span><span class="sxs-lookup"><span data-stu-id="58612-140">The example is for Google, which requires `PostLogoutRedirectUri`, `RedirectUri`, and `ResponseType`.</span></span> <span data-ttu-id="58612-141">При добавлении проверки подлинности в приложение вручную добавьте следующий код и конфигурацию в приложение со значениями заполнителей и другими значениями конфигурации.</span><span class="sxs-lookup"><span data-stu-id="58612-141">If adding authentication to an app, manually add the following code and configuration to the app with values for the placeholders and other configuration values.</span></span>

<span data-ttu-id="58612-142">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="58612-142">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="58612-143">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="58612-143">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="58612-144">Пример OIDC Google OAuth 2.0:</span><span class="sxs-lookup"><span data-stu-id="58612-144">Google OAuth 2.0 OIDC example:</span></span>

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

<span data-ttu-id="58612-145">URI перенаправления (`https://localhost:5001/authentication/login-callback`) регистрируется в [консоли API Google](https://console.developers.google.com/apis/dashboard) в разделе **Учетные данные** >  **`{NAME}`**  > **Разрешенные URI перенаправления**, где `{NAME}` — это имя клиента приложения в списке **идентификаторов клиентов OAuth 2.0** приложения в консоли Google API.</span><span class="sxs-lookup"><span data-stu-id="58612-145">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="58612-146">Поддержка проверки подлинности для изолированных приложений обеспечивается посредством OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="58612-146">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="58612-147">Метод <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="58612-147">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="58612-148">Значения, необходимые для настройки приложения, можно получить от поставщика удостоверений, совместимого с OIDC.</span><span class="sxs-lookup"><span data-stu-id="58612-148">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="58612-149">Получите эти значения при регистрации приложения, которая обычно производится на веб-портале.</span><span class="sxs-lookup"><span data-stu-id="58612-149">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="58612-150">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="58612-150">Access token scopes</span></span>

<span data-ttu-id="58612-151">Шаблон Blazor WebAssembly автоматически настраивает области по умолчанию для `openid` и `profile`.</span><span class="sxs-lookup"><span data-stu-id="58612-151">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="58612-152">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="58612-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="58612-153">Чтобы настроить маркер доступа в рамках потока данных для входа, добавьте область к областям маркера по умолчанию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span><span class="sxs-lookup"><span data-stu-id="58612-153">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span></span> <span data-ttu-id="58612-154">При добавлении проверки подлинности в приложение вручную добавьте следующий код и настройте URI области.</span><span class="sxs-lookup"><span data-stu-id="58612-154">If adding authentication to an app, manually add the following code and configure the scope URI.</span></span>

<span data-ttu-id="58612-155">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="58612-155">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="58612-156">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="58612-156">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="58612-157">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="58612-157">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="58612-158">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="58612-158">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="58612-159">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="58612-159">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="58612-160">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="58612-160">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="58612-161">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="58612-161">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="58612-162">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="58612-162">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="58612-163">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="58612-163">LoginDisplay component</span></span>

<span data-ttu-id="58612-164">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="58612-164">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="58612-165">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="58612-165">For authenticated users:</span></span>
  * <span data-ttu-id="58612-166">отображает имя текущего пользователя;</span><span class="sxs-lookup"><span data-stu-id="58612-166">Displays the current username.</span></span>
  * <span data-ttu-id="58612-167">отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="58612-167">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="58612-168">Для анонимных пользователей предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="58612-168">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="58612-169">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="58612-169">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="58612-170">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="58612-170">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="58612-171">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="58612-171">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="58612-172"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="58612-172"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="58612-173">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="58612-173">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="58612-174">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="58612-174">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
