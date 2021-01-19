---
title: ASP.NET Core Blazor WebAssembly с группами и ролями Azure Active Directory
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для использования групп и ролей Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058263"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="ed9e2-103">Группы, роли администратора и определяемые пользователем роли в Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="ed9e2-104">Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="ed9e2-105">Эта статья относится к приложениям Blazor ASP.NET Core версии 3.1 с Microsoft Identity версии 1.0, но мы планируем в ближайшее время ее обновить, чтобы она относилась к версии 5.0 с Identity версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="ed9e2-106">Дополнительные сведения см. в запросе на вытягивание и описании следующей проблемы GitHub.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="ed9e2-107">На вкладке **Измененные файлы** запроса на вытягивание содержатся черновик текста и примеры обновлений статьи.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="ed9e2-108">После проверки и применения окончательных обновлений запрос на вытягивание будет включен в интерактивный набор документации.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="ed9e2-109">Проблема. [Blazor WASM с группами и ролями AAD (dotnet/AspNetCore.Docs № 17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="ed9e2-110">Запрос на вытягивание: [Тема, посвященная группам и ролям BlazorAAD, версия 5.0 (dotnet/AspNetCore.Docs № 20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="ed9e2-111">Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="ed9e2-112">Определяемые пользователем группы</span><span class="sxs-lookup"><span data-stu-id="ed9e2-112">User-defined groups</span></span>
  * <span data-ttu-id="ed9e2-113">Безопасность</span><span class="sxs-lookup"><span data-stu-id="ed9e2-113">Security</span></span>
  * <span data-ttu-id="ed9e2-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="ed9e2-114">Microsoft 365</span></span>
  * <span data-ttu-id="ed9e2-115">Распределение</span><span class="sxs-lookup"><span data-stu-id="ed9e2-115">Distribution</span></span>
* <span data-ttu-id="ed9e2-116">Роли</span><span class="sxs-lookup"><span data-stu-id="ed9e2-116">Roles</span></span>
  * <span data-ttu-id="ed9e2-117">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="ed9e2-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="ed9e2-118">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="ed9e2-118">User-defined roles</span></span>

<span data-ttu-id="ed9e2-119">Рекомендации в этой статье относятся к сценариям развертывания Blazor WebAssembly AAD, описанным в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="ed9e2-120">Автономное развертывание с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="ed9e2-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="ed9e2-121">Автономное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="ed9e2-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="ed9e2-122">Размещенное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="ed9e2-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="ed9e2-123">Области действия</span><span class="sxs-lookup"><span data-stu-id="ed9e2-123">Scopes</span></span>

<span data-ttu-id="ed9e2-124">Для любого пользователя приложения с более чем пятью ролями администратора AAD и членством в группах безопасности требуется вызов [API Microsoft Graph](/graph/use-the-api).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-124">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="ed9e2-125">Чтобы разрешить API Graph вызовы, предоставьте автономному приложению или приложению *`Client`* размещенного решения Blazor любое из следующих [разрешений (областей) API Graph](/graph/permissions-reference) на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-125">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="ed9e2-126">`Directory.Read.All` является областью с наименьшими привилегиями и используется в примере, описываемом в этой статье.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-126">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="ed9e2-127">Определяемые пользователем группы и роли администратора</span><span class="sxs-lookup"><span data-stu-id="ed9e2-127">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="ed9e2-128">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `groups`, см. в следующих статьях Azure.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-128">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="ed9e2-129">Включите пользователей в определяемые пользователем группы AAD и роли администратора AAD.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-129">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="ed9e2-130">Роли, использующие группы безопасности Azure AD</span><span class="sxs-lookup"><span data-stu-id="ed9e2-130">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="ed9e2-131">Атрибут `groupMembershipClaims`</span><span class="sxs-lookup"><span data-stu-id="ed9e2-131">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="ed9e2-132">В следующих примерах предполагается, что пользователю назначена роль *Администратор выставления счетов*.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-132">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="ed9e2-133">Одно утверждение `groups`, отправленное AAD, представляет группы и роли пользователя в виде идентификаторов объектов (GUID) в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-133">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="ed9e2-134">Приложение должно преобразовать массив JSON групп и ролей в отдельные утверждения `group`, для которых приложение может создавать [политики](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-134">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="ed9e2-135">Когда число назначенных ролей администратора AAD и определяемых пользователем групп превысит пять, AAD отправит утверждение `hasgroups` со значением `true` вместо отправки утверждения `groups`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-135">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="ed9e2-136">Любое приложение, которое может иметь более пяти ролей и групп, назначенных пользователям, должно выполнять отдельный вызов API Graph для получения таких пользовательских ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-136">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="ed9e2-137">Пример реализации, приведенный в этой статье, посвящен этому сценарию.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-137">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="ed9e2-138">Дополнительные сведения см. в сведениях об утверждениях `groups` и `hasgroups` см. в статье [Маркеры доступа платформы идентификации Майкрософт. Утверждение полезных данных](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-138">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="ed9e2-139">Расширьте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства массива для групп и ролей.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-139">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="ed9e2-140">Присвойте каждому свойству пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach` позже.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-140">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="ed9e2-141">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-141">`CustomUserAccount.cs`:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ed9e2-142">Чтобы создать утверждения для групп и ролей AAD, используйте **любой** из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-142">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="ed9e2-143">Использование пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="ed9e2-143">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="ed9e2-144">Использование именованного элемента `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="ed9e2-144">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="ed9e2-145">Использование пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="ed9e2-145">Use the Graph SDK</span></span>

<span data-ttu-id="ed9e2-146">Добавьте ссылку на пакет в автономное приложение или приложение *`Client`* размещенного решения Blazor для [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-146">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="ed9e2-147">Добавьте служебные классы и конфигурацию Graph SDK, описанные в разделе *Пакет SDK для Graph* статьи <xref:blazor/security/webassembly/graph-api#graph-sdk>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-147">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="ed9e2-148">Добавьте следующую настраиваемую фабрику учетных записей пользователей в изолированное приложение или приложение *`Client`* размещенного решения Blazor (`CustomAccountFactory.cs`).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-148">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="ed9e2-149">Настраиваемая фабрика пользователей используется для обработки утверждений ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-149">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="ed9e2-150">Массив утверждений `roles` рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-150">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="ed9e2-151">Если имеется утверждение `hasgroups`, то пакет SDK для Graph используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-151">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="ed9e2-152">Приведенный выше код не включает в себя транзитивные членства.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-152">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="ed9e2-153">Если приложению требуются прямые и транзитивные утверждения на членство в группах:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-153">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="ed9e2-154">Измените тип `IUserMemberOfCollectionWithReferencesPage` с `groupsAndAzureRoles` на `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-154">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="ed9e2-155">При запросе групп и ролей пользователя замените свойство `MemberOf` на `TransitiveMemberOf`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-155">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="ed9e2-156">В `Program.Main` (`Program.cs`) настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей: Если приложение использует настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-156">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a><span data-ttu-id="ed9e2-157">Использование именованного элемента `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="ed9e2-157">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="ed9e2-158">В автономном приложении или приложении *`Client`* размещенного решения Blazor создайте пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-158">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="ed9e2-159">Используйте правильную область для вызовов API Graph, получающих сведения о ролях и группах.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-159">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="ed9e2-160">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-160">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

<span data-ttu-id="ed9e2-161">В `Program.Main` (`Program.cs`) добавьте службу реализации <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> и добавьте именованный <xref:System.Net.Http.HttpClient> для выполнения запросов API Graph.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-161">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="ed9e2-162">В следующем примере создается клиент с именем `GraphAPI`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-162">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="ed9e2-163">Создайте классы объектов каталога AAD для получения ролей и групп OData из вызова API Graph.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-163">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="ed9e2-164">Данные OData передаются в формате JSON, а вызов <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> заполняет экземпляр класса `DirectoryObjects`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-164">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="ed9e2-165">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-165">`DirectoryObjects.cs`:</span></span>

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

<span data-ttu-id="ed9e2-166">Создайте настраиваемую фабрику пользователей для обработки утверждений ролей и групп.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-166">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="ed9e2-167">В следующем примере реализации также обрабатывается массив утверждений `roles`, который рассматривается в разделе [Определяемые пользователем роли](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-167">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="ed9e2-168">Если имеется утверждение `hasgroups`, то именованный <xref:System.Net.Http.HttpClient> используется для создания разрешенного запроса API Graph на получение ролей и групп пользователей.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-168">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="ed9e2-169">В этой реализации используется конечная точка `https://graph.microsoft.com/v1.0/me/memberOf` Microsoft Identity Platform версии 1.0 ([документация по API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-169">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="ed9e2-170">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-170">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="ed9e2-171">Нет необходимости предоставлять код для удаления исходного утверждения `groups`, если оно есть, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-171">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="ed9e2-172">В этом примере:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-172">The approach in this example:</span></span>
>
> * <span data-ttu-id="ed9e2-173">добавляется пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для присоединения маркеров доступа к исходящим запросам;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-173">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="ed9e2-174">добавляется именованный <xref:System.Net.Http.HttpClient> для выполнения запросов веб-API к безопасной внешней конечной точке веб-API;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-174">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="ed9e2-175">используется именованный <xref:System.Net.Http.HttpClient> для выполнения разрешенных запросов.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-175">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="ed9e2-176">Более подробно этот подход рассматривается в статье <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-176">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="ed9e2-177">Зарегистрируйте фабрику в `Program.Main` (`Program.cs`) изолированного приложения или приложения *`Client`* размещенного решения Blazor.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-177">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="ed9e2-178">Согласие на область `Directory.Read.All` в качестве дополнительной области для приложения:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-178">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a><span data-ttu-id="ed9e2-179">Настройка авторизации</span><span class="sxs-lookup"><span data-stu-id="ed9e2-179">Authorization configuration</span></span>

<span data-ttu-id="ed9e2-180">Создайте [политики](xref:security/authorization/policies) для каждой группы или роли в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-180">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="ed9e2-181">В следующем примере создается политика для роли AAD *Администратор выставления счетов*:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-181">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="ed9e2-182">Полный список идентификаторов объектов ролей AAD см. в разделе [Идентификаторы объектов ролей администратора в AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-182">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="ed9e2-183">В следующих примерах приложение использует предыдущую политику для авторизации пользователя.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-183">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="ed9e2-184">С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):</span><span class="sxs-lookup"><span data-stu-id="ed9e2-184">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="ed9e2-185">Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="ed9e2-185">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="ed9e2-186">Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-186">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="ed9e2-187">Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-187">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="ed9e2-188">Авторизация доступа к API сервера для определяемых пользователем групп и ролей администратора</span><span class="sxs-lookup"><span data-stu-id="ed9e2-188">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="ed9e2-189">Кроме авторизации пользователей в приложении WebAssembly на стороне клиента для доступа к страницам и ресурсам, API сервера может выполнять авторизацию пользователей для доступа к защищенным конечным точкам API.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-189">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="ed9e2-190">После того как приложение *Server* проверит маркер доступа пользователя:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-190">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="ed9e2-191">Приложение API сервера использует неизменяемое [утверждение идентификатора объекта (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) пользователя из маркера доступа для получения маркера доступа для API Graph.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-191">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="ed9e2-192">Вызов API Graph получает определяемую пользователем Azure группу безопасности пользователя и членство для ролей администратора с помощью вызова метода пользователя [`memberOf`](/graph/api/user-list-memberof).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-192">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="ed9e2-193">Членство используется для реализации утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-193">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="ed9e2-194">[Политики авторизации](xref:security/authorization/policies) позволяют ограничить доступ пользователей к конечным точкам API сервера во всем приложении.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-194">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="ed9e2-195">Сейчас это руководство не предусматривает авторизацию пользователей на основе [определяемых пользователем ролей AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-195">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="ed9e2-196">Рекомендации в этом разделе описывают настройку приложения API сервера в качестве [*управляющей программы*](/azure/active-directory/develop/scenario-daemon-overview) для вызова API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-196">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="ed9e2-197">В этом подходе **не**:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-197">This approach does **not**:</span></span>

* <span data-ttu-id="ed9e2-198">требуется область `access_as_user`;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-198">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="ed9e2-199">выполняется доступ API Graph от имени пользователя или клиента, выполняющего запрос API.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-199">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="ed9e2-200">Для вызова API Graph приложением API сервера требуется только предоставление области API Graph **Приложение** уровня `Directory.Read.All` на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-200">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="ed9e2-201">Такой подход полностью запрещает клиентскому приложению доступ к данным каталога, которые API сервера не разрешает явно.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-201">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="ed9e2-202">Клиентское приложение может получить доступ только к конечным точкам контроллера приложения API сервера.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-202">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="ed9e2-203">Конфигурация Azure</span><span class="sxs-lookup"><span data-stu-id="ed9e2-203">Azure configuration</span></span>

* <span data-ttu-id="ed9e2-204">Убедитесь, что регистрации приложения *Server* предоставлена область API Graph **Приложение** (не **Делегат**) с уровнем `Directory.Read.All`, которая является уровнем доступа с минимальным набором прав для групп безопасности.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-204">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="ed9e2-205">Убедитесь, что согласие администратора применяется к области после создания назначения области.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-205">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="ed9e2-206">Назначьте приложению *Server* новый секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-206">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="ed9e2-207">Запишите секрет для конфигурации приложения в разделе [Параметры приложения](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-207">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="ed9e2-208">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="ed9e2-208">App settings</span></span>

<span data-ttu-id="ed9e2-209">В файле параметров приложения (`appsettings.json` или `appsettings.Production.json`) создайте запись `ClientSecret` с секретом приложения *Server* с портала Azure:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-209">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="ed9e2-210">Пример:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-210">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ed9e2-211">Если домен издателя клиента не проверен, в области API сервера для доступа пользователя или клиента используется универсальный код ресурса (URI) на основе `https://`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-211">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="ed9e2-212">В этом сценарии приложению API сервера требуется конфигурация `Audience` в файле `appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-212">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="ed9e2-213">В следующей конфигурации конец значения `Audience` **не** включает область по умолчанию `/{DEFAULT SCOPE}`, где заполнитель `{DEFAULT SCOPE}` является областью по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-213">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="ed9e2-214">В приведенном выше примере конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-214">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="ed9e2-215">домен клиента `contoso.onmicrosoft.com`;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-215">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="ed9e2-216">`ClientId` приложения API сервера `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-216">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="ed9e2-217">Настройка `Audience` явным образом обычно **не** требуется для приложения с проверенным доменом издателя с областью API на основе `api://`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-217">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="ed9e2-218">Для получения дополнительной информации см. <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-218">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="ed9e2-219">Политики авторизации,</span><span class="sxs-lookup"><span data-stu-id="ed9e2-219">Authorization policies</span></span>

<span data-ttu-id="ed9e2-220">Создайте [политики авторизации](xref:security/authorization/policies) для групп безопасности AAD и ролей администратора AAD в методе `Startup.ConfigureServices` приложения *Server* (`Startup.cs`) на основе идентификаторов объектов групп и [идентификаторов объектов ролей администратора AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-220">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="ed9e2-221">Например, политика роли администратора выставления счетов в Azure имеет следующую конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-221">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="ed9e2-222">Для получения дополнительной информации см. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-222">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="ed9e2-223">Доступ к контроллеру</span><span class="sxs-lookup"><span data-stu-id="ed9e2-223">Controller access</span></span>

<span data-ttu-id="ed9e2-224">Задайте обязательные политики на контроллерах приложения *Server*.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-224">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="ed9e2-225">Следующий пример ограничивает доступ к данным выставления счетов с `BillingDataController` для администраторов выставления счетов Azure с использованием имени политики `BillingAdmin`, которое задано в разделе [Политики авторизации](#authorization-policies):</span><span class="sxs-lookup"><span data-stu-id="ed9e2-225">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="ed9e2-226">Пакеты</span><span class="sxs-lookup"><span data-stu-id="ed9e2-226">Packages</span></span>

<span data-ttu-id="ed9e2-227">Добавьте в приложение *Server* ссылки на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-227">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="ed9e2-228">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="ed9e2-228">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="ed9e2-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="ed9e2-230">Службы</span><span class="sxs-lookup"><span data-stu-id="ed9e2-230">Services</span></span>

<span data-ttu-id="ed9e2-231">Код в методе `Startup.ConfigureServices` класса `Startup` приложения *Server* требует дополнительные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-231">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="ed9e2-232">Добавьте следующие пространства имен в `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-232">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="ed9e2-233">При настройке <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-233">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="ed9e2-234">При необходимости включите обработку для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-234">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ed9e2-235">Например, приложение может регистрировать неудачные попытки аутентификации.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-235">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="ed9e2-236">В <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> создайте вызов API Graph для получения групп и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="ed9e2-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> предоставляет персональные данные в записях сообщений в журнале.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="ed9e2-238">Активируйте персональные данные только для отладки в тестовых учетных записях пользователей.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-238">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="ed9e2-239">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-239">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="ed9e2-240">В приведенном выше коде обработка следующих ошибок токена является необязательной:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-240">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="ed9e2-241">`MsalUiRequiredException`: приложение не имеет достаточных разрешений (областей).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-241">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="ed9e2-242">Убедитесь, что области приложения API сервера на портале Azure включают разрешение **Приложение** для `Directory.Read.All`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-242">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="ed9e2-243">Убедитесь, что администратор клиента предоставил разрешения для приложения.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-243">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="ed9e2-244">`MsalServiceException` (`AADSTS70011`): Убедитесь, что областью является `https://graph.microsoft.com/.default`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-244">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="ed9e2-245">Пакеты</span><span class="sxs-lookup"><span data-stu-id="ed9e2-245">Packages</span></span>

<span data-ttu-id="ed9e2-246">Добавьте в приложение *Server* ссылки на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-246">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="ed9e2-247">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="ed9e2-247">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="ed9e2-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="ed9e2-249">Конфигурация службы</span><span class="sxs-lookup"><span data-stu-id="ed9e2-249">Service configuration</span></span>

<span data-ttu-id="ed9e2-250">Добавьте логику в метод `Startup.ConfigureServices` приложения *Server*, чтобы API Graph вызывал и реализовывал утверждения `group` для групп безопасности и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-250">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="ed9e2-251">В примере кода в этом разделе используется библиотека проверки подлинности Active Directory (ADAL), которая основана на платформе Microsoft Identity Platform версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-251">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="ed9e2-252">Код в классе `Startup` приложения *Server* требует дополнительные пространства имен.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-252">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="ed9e2-253">Следующий набор операторов `using` включает необходимые пространства имен для кода, который приведен в этом разделе:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-253">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="ed9e2-254">При настройке <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-254">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="ed9e2-255">При необходимости включите обработку для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-255">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ed9e2-256">Например, приложение может регистрировать неудачные попытки аутентификации.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-256">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="ed9e2-257">В <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> создайте вызов API Graph для получения групп и ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="ed9e2-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> предоставляет персональные данные в записях сообщений в журнале.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="ed9e2-259">Активируйте персональные данные только для отладки в тестовых учетных записях пользователей.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-259">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="ed9e2-260">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-260">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="ed9e2-261">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-261">In the preceding example:</span></span>

* <span data-ttu-id="ed9e2-262">Автоматическое получение маркера (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) выполняется первым, так как маркер доступа может быть уже сохранен в кэше маркеров ADAL.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-262">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="ed9e2-263">Получить маркер из кэша быстрее, чем запросить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-263">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="ed9e2-264">Если маркер доступа не получен из кэша (возникает исключение <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> или <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType>), операция утверждения пользователя (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) выполняется с учетными данными клиента (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) для получения маркера от имени пользователя (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-264">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="ed9e2-265">Затем `Microsoft.Graph.GraphServiceClient` может выполнить вызов API Graph с помощью маркера.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-265">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="ed9e2-266">Маркер помещается в кэш маркеров ADAL.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-266">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="ed9e2-267">При будущих вызовах API Graph к этому же пользователю маркер будет автоматически получен из кэша с помощью <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-267">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="ed9e2-268">Код в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> не получает сведения о временном членстве.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="ed9e2-269">Чтобы изменить код для получения сведений о непосредственном и временном членстве, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-269">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="ed9e2-270">Для строки кода:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-270">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="ed9e2-271">Замените предыдущую строку следующим:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-271">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="ed9e2-272">Для строки кода:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-272">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="ed9e2-273">Замените предыдущую строку следующим:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-273">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="ed9e2-274">Код в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> не различает группы безопасности AAD и роли администратора AAD при создании утверждений.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-274">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="ed9e2-275">Чтобы приложение различало группы и роли, проверьте `entry.ODataType` при итерации по группам и ролям.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-275">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="ed9e2-276">Чтобы создать отдельные утверждения для групп безопасности и ролей, используйте код, аналогичный приведенному ниже:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-276">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="ed9e2-277">Определяемые пользователем роли</span><span class="sxs-lookup"><span data-stu-id="ed9e2-277">User-defined roles</span></span>

<span data-ttu-id="ed9e2-278">Приложение, зарегистрированное в AAD, можно также настроить для использования определяемых пользователем ролей.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-278">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="ed9e2-279">Сведения о том, как настроить приложение на портале Azure для предоставления утверждения о членстве `roles`, см. в разделе [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-279">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="ed9e2-280">В следующем примере предполагается, что приложение настроено с двумя ролями:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-280">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="ed9e2-281">Несмотря на то, что вы не можете назначать роли группам безопасности без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение `roles` для пользователей с помощью стандартной учетной записи Azure.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-281">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="ed9e2-282">В рекомендациях в данном разделе не требуется учетная запись Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-282">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="ed9e2-283">На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-283">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="ed9e2-284">Одно утверждение `roles`, отправленное AAD, представляет определяемые пользователем роли как `appRoles` `value` в массиве JSON.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-284">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="ed9e2-285">Приложение должно преобразовать массив JSON из ролей в индивидуальные утверждения `role`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-285">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="ed9e2-286">Класс `CustomUserFactory`, приведенный в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles), настроен на выполнение действий с утверждением `roles` с использованием значения массива JSON.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-286">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="ed9e2-287">Выполните добавление и регистрацию `CustomUserFactory` в изолированном приложении или приложении *`Client`* размещенного решения Blazor, как показано в разделе [Определяемые пользователями группы и роли администратора AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-287">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="ed9e2-288">Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-288">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="ed9e2-289">В `Program.Main` изолированного приложения или приложения *`Client`* размещенного решения Blazor укажите утверждение с именем `role` в качестве утверждения роли:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-289">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="ed9e2-290">На этом этапе можно применять подходы с авторизацией компонентов.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-290">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="ed9e2-291">Любой из механизмов авторизации в компонентах может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-291">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="ed9e2-292">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-292">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="ed9e2-293">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-293">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="ed9e2-294">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="ed9e2-294">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="ed9e2-295">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="ed9e2-295">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="ed9e2-296">Идентификаторы объектов ролей администратора AAD</span><span class="sxs-lookup"><span data-stu-id="ed9e2-296">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="ed9e2-297">Идентификаторы объектов, приведенные в следующей таблице, используются для создания [политик](xref:security/authorization/policies) для утверждений `group`.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-297">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="ed9e2-298">Политики позволяют приложению авторизовать пользователей для различных действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-298">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="ed9e2-299">Дополнительные сведения см. в разделе [Определяемые пользователем группы и роли администратора AAD](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="ed9e2-299">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="ed9e2-300">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="ed9e2-300">AAD Administrator Role</span></span> | <span data-ttu-id="ed9e2-301">Идентификатор объекта.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-301">Object ID</span></span>
--- | ---
<span data-ttu-id="ed9e2-302">администратор приложений;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-302">Application administrator</span></span> | <span data-ttu-id="ed9e2-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="ed9e2-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="ed9e2-304">Разработчик приложений</span><span class="sxs-lookup"><span data-stu-id="ed9e2-304">Application developer</span></span> | <span data-ttu-id="ed9e2-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="ed9e2-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="ed9e2-306">Администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ed9e2-306">Authentication administrator</span></span> | <span data-ttu-id="ed9e2-307">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="ed9e2-307">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="ed9e2-308">Администратор Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="ed9e2-308">Azure DevOps administrator</span></span> | <span data-ttu-id="ed9e2-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="ed9e2-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="ed9e2-310">Администратор Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="ed9e2-310">Azure Information Protection administrator</span></span> | <span data-ttu-id="ed9e2-311">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="ed9e2-311">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="ed9e2-312">Администратор наборов ключей IEF B2C</span><span class="sxs-lookup"><span data-stu-id="ed9e2-312">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="ed9e2-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="ed9e2-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="ed9e2-314">Администратор политик IEF B2C</span><span class="sxs-lookup"><span data-stu-id="ed9e2-314">B2C IEF Policy administrator</span></span> | <span data-ttu-id="ed9e2-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="ed9e2-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="ed9e2-316">Администратор потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="ed9e2-316">B2C user flow administrator</span></span> | <span data-ttu-id="ed9e2-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="ed9e2-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="ed9e2-318">Администратор атрибутов потоков пользователей B2C</span><span class="sxs-lookup"><span data-stu-id="ed9e2-318">B2C user flow attribute administrator</span></span> | <span data-ttu-id="ed9e2-319">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="ed9e2-319">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="ed9e2-320">Администратор выставления счетов</span><span class="sxs-lookup"><span data-stu-id="ed9e2-320">Billing administrator</span></span> | <span data-ttu-id="ed9e2-321">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="ed9e2-321">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="ed9e2-322">Администратор облачных приложений</span><span class="sxs-lookup"><span data-stu-id="ed9e2-322">Cloud application administrator</span></span> | <span data-ttu-id="ed9e2-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="ed9e2-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="ed9e2-324">Администратор облачного устройства</span><span class="sxs-lookup"><span data-stu-id="ed9e2-324">Cloud device administrator</span></span> | <span data-ttu-id="ed9e2-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="ed9e2-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="ed9e2-326">администратор соответствия требованиям.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-326">Compliance administrator</span></span> | <span data-ttu-id="ed9e2-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="ed9e2-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="ed9e2-328">Администратор данных соответствия</span><span class="sxs-lookup"><span data-stu-id="ed9e2-328">Compliance data administrator</span></span> | <span data-ttu-id="ed9e2-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="ed9e2-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="ed9e2-330">Администратор условного доступа</span><span class="sxs-lookup"><span data-stu-id="ed9e2-330">Conditional Access administrator</span></span> | <span data-ttu-id="ed9e2-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="ed9e2-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="ed9e2-332">лицо, утверждающее доступ клиентов к LockBox;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-332">Customer LockBox access approver</span></span> | <span data-ttu-id="ed9e2-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="ed9e2-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="ed9e2-334">Администратор аналитики классических приложений</span><span class="sxs-lookup"><span data-stu-id="ed9e2-334">Desktop Analytics administrator</span></span> | <span data-ttu-id="ed9e2-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="ed9e2-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="ed9e2-336">Читатели каталога</span><span class="sxs-lookup"><span data-stu-id="ed9e2-336">Directory readers</span></span> | <span data-ttu-id="ed9e2-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="ed9e2-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="ed9e2-338">Администратор Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="ed9e2-338">Dynamics 365 administrator</span></span> | <span data-ttu-id="ed9e2-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="ed9e2-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="ed9e2-340">администратор Exchange;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-340">Exchange administrator</span></span> | <span data-ttu-id="ed9e2-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="ed9e2-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="ed9e2-342">Администратор внешнего поставщика Identity</span><span class="sxs-lookup"><span data-stu-id="ed9e2-342">External Identity Provider administrator</span></span> | <span data-ttu-id="ed9e2-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="ed9e2-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="ed9e2-344">Глобальный администратор.</span><span class="sxs-lookup"><span data-stu-id="ed9e2-344">Global administrator</span></span> | <span data-ttu-id="ed9e2-345">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="ed9e2-345">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="ed9e2-346">Глобальный читатель</span><span class="sxs-lookup"><span data-stu-id="ed9e2-346">Global reader</span></span> | <span data-ttu-id="ed9e2-347">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="ed9e2-347">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="ed9e2-348">Администратор групп</span><span class="sxs-lookup"><span data-stu-id="ed9e2-348">Groups administrator</span></span> | <span data-ttu-id="ed9e2-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="ed9e2-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="ed9e2-350">Приглашающий гостей</span><span class="sxs-lookup"><span data-stu-id="ed9e2-350">Guest inviter</span></span> | <span data-ttu-id="ed9e2-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="ed9e2-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="ed9e2-352">администратор службы технической поддержки;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-352">Helpdesk administrator</span></span> | <span data-ttu-id="ed9e2-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="ed9e2-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="ed9e2-354">Администратор Intune</span><span class="sxs-lookup"><span data-stu-id="ed9e2-354">Intune administrator</span></span> | <span data-ttu-id="ed9e2-355">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="ed9e2-355">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="ed9e2-356">Администратор Kaizala</span><span class="sxs-lookup"><span data-stu-id="ed9e2-356">Kaizala administrator</span></span> | <span data-ttu-id="ed9e2-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="ed9e2-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="ed9e2-358">Администратор лицензий</span><span class="sxs-lookup"><span data-stu-id="ed9e2-358">License administrator</span></span> | <span data-ttu-id="ed9e2-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="ed9e2-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="ed9e2-360">Читатель конфиденциальности данных Центра сообщений</span><span class="sxs-lookup"><span data-stu-id="ed9e2-360">Message center privacy reader</span></span> | <span data-ttu-id="ed9e2-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="ed9e2-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="ed9e2-362">Читатель центра сообщений</span><span class="sxs-lookup"><span data-stu-id="ed9e2-362">Message center reader</span></span> | <span data-ttu-id="ed9e2-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="ed9e2-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="ed9e2-364">Администратор приложений Office</span><span class="sxs-lookup"><span data-stu-id="ed9e2-364">Office apps administrator</span></span> | <span data-ttu-id="ed9e2-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="ed9e2-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="ed9e2-366">Администратор паролей</span><span class="sxs-lookup"><span data-stu-id="ed9e2-366">Password administrator</span></span> | <span data-ttu-id="ed9e2-367">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="ed9e2-367">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="ed9e2-368">Администратор Power BI</span><span class="sxs-lookup"><span data-stu-id="ed9e2-368">Power BI administrator</span></span> | <span data-ttu-id="ed9e2-369">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="ed9e2-369">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="ed9e2-370">Администратор Power Platform</span><span class="sxs-lookup"><span data-stu-id="ed9e2-370">Power platform administrator</span></span> | <span data-ttu-id="ed9e2-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="ed9e2-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="ed9e2-372">Привилегированный администратор проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ed9e2-372">Privileged authentication administrator</span></span> | <span data-ttu-id="ed9e2-373">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="ed9e2-373">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="ed9e2-374">администратор привилегированных ролей;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-374">Privileged role administrator</span></span> | <span data-ttu-id="ed9e2-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="ed9e2-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="ed9e2-376">Читатель отчетов</span><span class="sxs-lookup"><span data-stu-id="ed9e2-376">Reports reader</span></span> | <span data-ttu-id="ed9e2-377">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="ed9e2-377">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="ed9e2-378">Администратор поиска</span><span class="sxs-lookup"><span data-stu-id="ed9e2-378">Search administrator</span></span> | <span data-ttu-id="ed9e2-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="ed9e2-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="ed9e2-380">Редактор поиска</span><span class="sxs-lookup"><span data-stu-id="ed9e2-380">Search editor</span></span> | <span data-ttu-id="ed9e2-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="ed9e2-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="ed9e2-382">администратор безопасности;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-382">Security administrator</span></span> | <span data-ttu-id="ed9e2-383">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="ed9e2-383">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="ed9e2-384">Оператор безопасности</span><span class="sxs-lookup"><span data-stu-id="ed9e2-384">Security operator</span></span> | <span data-ttu-id="ed9e2-385">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="ed9e2-385">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="ed9e2-386">Читатель сведений о безопасности</span><span class="sxs-lookup"><span data-stu-id="ed9e2-386">Security reader</span></span> | <span data-ttu-id="ed9e2-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="ed9e2-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="ed9e2-388">администратор службы поддержки;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-388">Service support administrator</span></span> | <span data-ttu-id="ed9e2-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="ed9e2-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="ed9e2-390">администратор SharePoint;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-390">SharePoint administrator</span></span> | <span data-ttu-id="ed9e2-391">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="ed9e2-391">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="ed9e2-392">администратор Skype для бизнеса;</span><span class="sxs-lookup"><span data-stu-id="ed9e2-392">Skype for Business administrator</span></span> | <span data-ttu-id="ed9e2-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="ed9e2-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="ed9e2-394">Администратор связи Teams</span><span class="sxs-lookup"><span data-stu-id="ed9e2-394">Teams Communications Administrator</span></span> | <span data-ttu-id="ed9e2-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="ed9e2-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="ed9e2-396">Инженер службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="ed9e2-396">Teams Communications Support Engineer</span></span> | <span data-ttu-id="ed9e2-397">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="ed9e2-397">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="ed9e2-398">Специалист службы поддержки связи Teams</span><span class="sxs-lookup"><span data-stu-id="ed9e2-398">Teams Communications Specialist</span></span> | <span data-ttu-id="ed9e2-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="ed9e2-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="ed9e2-400">Администратор службы Teams</span><span class="sxs-lookup"><span data-stu-id="ed9e2-400">Teams Service Administrator</span></span> | <span data-ttu-id="ed9e2-401">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="ed9e2-401">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="ed9e2-402">Администратор пользователей</span><span class="sxs-lookup"><span data-stu-id="ed9e2-402">User administrator</span></span> | <span data-ttu-id="ed9e2-403">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="ed9e2-403">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed9e2-404">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ed9e2-404">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
