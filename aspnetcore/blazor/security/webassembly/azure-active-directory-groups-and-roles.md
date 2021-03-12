---
title: ASP.NET Core Blazor WebAssembly с группами и ролями Azure Active Directory
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для использования групп и ролей Azure Active Directory.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
ms.openlocfilehash: b725a60a310be23f7ceb626d4c543d0df6fadf62
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394763"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="0fb58-103">Группы, роли администратора и роли приложения в Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="0fb58-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="0fb58-104">Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="0fb58-104">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="0fb58-105">Группы</span><span class="sxs-lookup"><span data-stu-id="0fb58-105">Groups</span></span>
  * <span data-ttu-id="0fb58-106">Безопасность</span><span class="sxs-lookup"><span data-stu-id="0fb58-106">Security</span></span>
  * <span data-ttu-id="0fb58-107">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="0fb58-107">Microsoft 365</span></span>
  * <span data-ttu-id="0fb58-108">Распределение</span><span class="sxs-lookup"><span data-stu-id="0fb58-108">Distribution</span></span>
* <span data-ttu-id="0fb58-109">Роли</span><span class="sxs-lookup"><span data-stu-id="0fb58-109">Roles</span></span>
  * <span data-ttu-id="0fb58-110">Роли администратора в AAD</span><span class="sxs-lookup"><span data-stu-id="0fb58-110">AAD Administrator Roles</span></span>
  * <span data-ttu-id="0fb58-111">Роли приложения</span><span class="sxs-lookup"><span data-stu-id="0fb58-111">App Roles</span></span>

<span data-ttu-id="0fb58-112">Рекомендации в этой статье относятся к сценариям развертывания Blazor WebAssembly AAD, описанным в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="0fb58-112">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="0fb58-113">Автономное развертывание с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="0fb58-113">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="0fb58-114">Автономное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="0fb58-114">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="0fb58-115">Размещенное развертывание с помощью AAD</span><span class="sxs-lookup"><span data-stu-id="0fb58-115">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="0fb58-116">В этой статье приведены инструкции для клиентских и серверных приложений.</span><span class="sxs-lookup"><span data-stu-id="0fb58-116">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="0fb58-117">**Клиент:** отдельные приложения Blazor WebAssembly или приложение **`Client`** размещенного решения Blazor.</span><span class="sxs-lookup"><span data-stu-id="0fb58-117">**CLIENT**: Standalone Blazor WebAssembly apps or the **`Client`** app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="0fb58-118">**Сервер:** отдельные приложения API или веб-API сервера ASP.NET Core или приложение **`Server`** размещенного решения Blazor.</span><span class="sxs-lookup"><span data-stu-id="0fb58-118">**SERVER**: Standalone ASP.NET Core server API/web API apps or the **`Server`** app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="0fb58-119">Области действия</span><span class="sxs-lookup"><span data-stu-id="0fb58-119">Scopes</span></span>

<span data-ttu-id="0fb58-120">Чтобы разрешить вызовы [API Microsoft Graph](/graph/use-the-api) для получения профиля пользователя, назначения ролей и данных о членстве в группах, **клиентское** приложение настраивается с помощью (`https://graph.microsoft.com/User.Read`) [разрешений API Graph (область)](/graph/permissions-reference) на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-120">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="0fb58-121">**Серверное** приложение, которое вызывает API Graph для получения данных членства в ролях и группах, настраивается с помощью [разрешения API Graph (область)](/graph/permissions-reference) `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-121">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="0fb58-122">Эти области используются наряду с областями, требуемыми в сценариях развертывания AAD, как описано в первом разделе этой статьи.</span><span class="sxs-lookup"><span data-stu-id="0fb58-122">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="0fb58-123">Слова "разрешение" и "область" являются взаимозаменяемыми в контексте портала Azure, а также разных наборов документации Майкрософт и сторонней документации.</span><span class="sxs-lookup"><span data-stu-id="0fb58-123">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="0fb58-124">В этой статье для всех разрешений, назначенных приложению на портале Azure, используется слово "область".</span><span class="sxs-lookup"><span data-stu-id="0fb58-124">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="0fb58-125">Атрибут утверждений членства в группах</span><span class="sxs-lookup"><span data-stu-id="0fb58-125">Group Membership Claims attribute</span></span>

<span data-ttu-id="0fb58-126">В манифесте приложения на портале Azure для **клиентского** и **серверного** приложений установите для [атрибута `groupMembershipClaims`](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) значение `All`.</span><span class="sxs-lookup"><span data-stu-id="0fb58-126">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="0fb58-127">Значение `All` позволяет получить все группы безопасности, группы рассылки и роли, членом которых является вошедший пользователь.</span><span class="sxs-lookup"><span data-stu-id="0fb58-127">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="0fb58-128">Откройте регистрацию приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-128">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="0fb58-129">Выберите **Управление** > **Манифест** на панели сбоку.</span><span class="sxs-lookup"><span data-stu-id="0fb58-129">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="0fb58-130">Найдите атрибут `groupMembershipClaims`.</span><span class="sxs-lookup"><span data-stu-id="0fb58-130">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="0fb58-131">Присвойте ему значение `All`.</span><span class="sxs-lookup"><span data-stu-id="0fb58-131">Set the value to `All`.</span></span>
1. <span data-ttu-id="0fb58-132">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="0fb58-132">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="0fb58-133">Настраиваемая учетная запись пользователя</span><span class="sxs-lookup"><span data-stu-id="0fb58-133">Custom user account</span></span>

<span data-ttu-id="0fb58-134">Назначьте пользователей группам безопасности AAD и ролям администратора AAD на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-134">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="0fb58-135">Примеры в этой статье:</span><span class="sxs-lookup"><span data-stu-id="0fb58-135">The examples in this article:</span></span>

* <span data-ttu-id="0fb58-136">Предположим, что в арендаторе AAD на портале Azure пользователю назначена роль AAD *Администратор выставления счетов* для авторизации и получения доступа к данным API сервера.</span><span class="sxs-lookup"><span data-stu-id="0fb58-136">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="0fb58-137">Используйте [политики авторизации](xref:security/authorization/policies) для управления доступом в **клиентском** и **серверном** приложении.</span><span class="sxs-lookup"><span data-stu-id="0fb58-137">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="0fb58-138">В **клиентском** приложении обновите <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства для:</span><span class="sxs-lookup"><span data-stu-id="0fb58-138">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="0fb58-139">`Roles`: массива ролей приложения AAD (см. раздел [Роли приложения](#app-roles)).</span><span class="sxs-lookup"><span data-stu-id="0fb58-139">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="0fb58-140">`Wids`: ролей администратора AAD в [утверждениях известных идентификаторов (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="0fb58-140">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="0fb58-141">`Oid`: неизменяемого [утверждения идентификатора объекта (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (уникально идентифицирует пользователя в пределах и за пределами арендаторов).</span><span class="sxs-lookup"><span data-stu-id="0fb58-141">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="0fb58-142">Присвойте каждому свойству массива пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach`.</span><span class="sxs-lookup"><span data-stu-id="0fb58-142">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="0fb58-143">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-143">`CustomUserAccount.cs`:</span></span>

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

<span data-ttu-id="0fb58-144">Добавьте ссылку на пакет в файл проекта **клиентского** приложения для [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span><span class="sxs-lookup"><span data-stu-id="0fb58-144">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="0fb58-145">Добавьте служебные классы и конфигурацию Graph SDK, описанные в разделе *Пакет SDK для Graph* статьи <xref:blazor/security/webassembly/graph-api#graph-sdk>.</span><span class="sxs-lookup"><span data-stu-id="0fb58-145">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="0fb58-146">В классе `GraphClientExtensions` укажите область `User.Read` маркера доступа в методе `AuthenticateRequestAsync`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-146">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="0fb58-147">Добавьте следующую настраиваемую фабрику учетных записей пользователей в **клиентское** приложение.</span><span class="sxs-lookup"><span data-stu-id="0fb58-147">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="0fb58-148">Эта фабрика используется для установки:</span><span class="sxs-lookup"><span data-stu-id="0fb58-148">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="0fb58-149">утверждений роли приложения (`appRole`) (см. раздел [Роли приложения](#app-roles));</span><span class="sxs-lookup"><span data-stu-id="0fb58-149">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="0fb58-150">утверждений роли администратора AAD (`directoryRole`);</span><span class="sxs-lookup"><span data-stu-id="0fb58-150">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="0fb58-151">примера утверждения данных профиля пользователя для номера мобильного телефона пользователя (`mobilePhone`);</span><span class="sxs-lookup"><span data-stu-id="0fb58-151">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="0fb58-152">утверждений группы AAD (`directoryGroup`).</span><span class="sxs-lookup"><span data-stu-id="0fb58-152">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="0fb58-153">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-153">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
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
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="0fb58-154">Приведенный выше код не включает в себя транзитивные членства.</span><span class="sxs-lookup"><span data-stu-id="0fb58-154">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="0fb58-155">Если приложению требуются прямые и транзитивные утверждения членства в группах, замените свойство `MemberOf` (`IUserMemberOfCollectionWithReferencesRequestBuilder`) на `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span><span class="sxs-lookup"><span data-stu-id="0fb58-155">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="0fb58-156">Приведенный выше код не учитывает утверждения членства в группах (`groups`), которые являются ролями администратора AAD (тип `#microsoft.graph.directoryRole`), так как значения GUID, возвращаемые платформой Identity 2.0 (Майкрософт), являются **идентификаторами сущностей** ролей администратора AAD, но не [**идентификаторами шаблонов ролей**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="0fb58-156">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="0fb58-157">Идентификаторы сущностей не являются стабильными для арендаторов на платформе Identity 2.0 (Майкрософт) и не должны использоваться для создания политик авторизации для пользователей в приложениях.</span><span class="sxs-lookup"><span data-stu-id="0fb58-157">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="0fb58-158">Всегда используйте **идентификаторы шаблонов ролей** для ролей администратора AAD, **предоставляемые утверждениями `wids`** .</span><span class="sxs-lookup"><span data-stu-id="0fb58-158">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="0fb58-159">В `Program.Main` **клиентского** приложения настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей.</span><span class="sxs-lookup"><span data-stu-id="0fb58-159">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="0fb58-160">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-160">`Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a><span data-ttu-id="0fb58-161">Настройка авторизации</span><span class="sxs-lookup"><span data-stu-id="0fb58-161">Authorization configuration</span></span>

<span data-ttu-id="0fb58-162">В **клиентском** приложении создайте [политику](xref:security/authorization/policies) для каждой [роли приложения](#app-roles), роли администратора AAD или группы безопасности в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="0fb58-162">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="0fb58-163">В следующем примере создается политика для роли AAD *Администратор выставления счетов*:</span><span class="sxs-lookup"><span data-stu-id="0fb58-163">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="0fb58-164">Полный список идентификаторов для ролей администратора AAD см. в разделе [Идентификаторы шаблонов ролей](/azure/active-directory/roles/permissions-reference#role-template-ids) в документации Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-164">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="0fb58-165">Дополнительные сведения о политиках авторизации см. здесь: <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="0fb58-165">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0fb58-166">В следующих примерах **клиентское** приложение использует предыдущую политику для авторизации пользователя.</span><span class="sxs-lookup"><span data-stu-id="0fb58-166">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="0fb58-167">С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):</span><span class="sxs-lookup"><span data-stu-id="0fb58-167">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="0fb58-168">Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span><span class="sxs-lookup"><span data-stu-id="0fb58-168">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="0fb58-169">Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.</span><span class="sxs-lookup"><span data-stu-id="0fb58-169">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="0fb58-170">Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="0fb58-170">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="0fb58-171">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-171">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="0fb58-172">Авторизация доступа к API и веб-API сервера</span><span class="sxs-lookup"><span data-stu-id="0fb58-172">Authorize server API/web API access</span></span>

<span data-ttu-id="0fb58-173">**Серверное** приложение API может авторизовать пользователей для доступа к защищенным конечным точкам API с помощью [политик авторизации](xref:security/authorization/policies) для групп безопасности, ролей администратора AAD и ролей приложений, если маркер доступа содержит утверждения `groups`, `wids` и `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`.</span><span class="sxs-lookup"><span data-stu-id="0fb58-173">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="0fb58-174">В следующем примере создается политика для роли AAD *Администратор выставления счетов* в `Startup.ConfigureServices` с использованием утверждений `wids` (известные идентификаторы и идентификаторы шаблонов ролей):</span><span class="sxs-lookup"><span data-stu-id="0fb58-174">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="0fb58-175">Полный список идентификаторов для ролей администратора AAD см. в разделе [Идентификаторы шаблонов ролей](/azure/active-directory/roles/permissions-reference#role-template-ids) в документации Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-175">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="0fb58-176">Дополнительные сведения о политиках авторизации см. здесь: <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="0fb58-176">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0fb58-177">Доступ к контроллеру в **серверном** приложении может быть основан на использовании [атрибута `[Authorize]`](xref:security/authorization/simple) с именем политики (документация по API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span><span class="sxs-lookup"><span data-stu-id="0fb58-177">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="0fb58-178">Следующий пример ограничивает доступ к данным выставления счетов с `BillingDataController` для администраторов выставления счетов Azure с использованием имени политики `BillingAdministrator`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-178">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

<span data-ttu-id="0fb58-179">Для получения дополнительной информации см. <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="0fb58-179">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="0fb58-180">Роли приложения</span><span class="sxs-lookup"><span data-stu-id="0fb58-180">App Roles</span></span>

<span data-ttu-id="0fb58-181">Сведения о том, как настроить приложение на портале Azure для предоставления утверждений членства ролей приложения, см. в статье [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-181">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="0fb58-182">В следующем примере предполагается, что **клиентское** и **серверное** приложения настроены с помощью двух ролей, а роли назначены тестовому пользователю:</span><span class="sxs-lookup"><span data-stu-id="0fb58-182">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="0fb58-183">При разработке размещенного приложения Blazor WebAssembly или пары отдельных приложений "клиент — сервер" (отдельное приложение Blazor WebAssembly и отдельное приложение API или веб-API сервера ASP.NET Core), свойство манифеста `appRoles` регистраций клиентского и серверного приложений на портале Azure должно включать одни и те же настроенные роли.</span><span class="sxs-lookup"><span data-stu-id="0fb58-183">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="0fb58-184">Установив роли в манифесте клиентского приложения, скопируйте их целиком в манифест серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="0fb58-184">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="0fb58-185">Если не создать зеркальную копию манифеста `appRoles` между регистрацией клиентских и серверных приложений, утверждения ролей не будут установлены для пользователей API или веб-API сервера, прошедших проверку подлинности, даже если их маркер доступа имеет правильные утверждения ролей.</span><span class="sxs-lookup"><span data-stu-id="0fb58-185">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="0fb58-186">Хотя вы не можете назначать роли группам без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение ролей для пользователей с помощью стандартной учетной записи Azure.</span><span class="sxs-lookup"><span data-stu-id="0fb58-186">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="0fb58-187">В рекомендациях в этом разделе не предполагается использовать учетную запись AAD Premium.</span><span class="sxs-lookup"><span data-stu-id="0fb58-187">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="0fb58-188">На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.</span><span class="sxs-lookup"><span data-stu-id="0fb58-188">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="0fb58-189">`CustomAccountFactory`, как показано в разделе [Настраиваемая учетная запись пользователя](#custom-user-account), настраивается для работы в утверждении `roles` с использованием значения массива JSON.</span><span class="sxs-lookup"><span data-stu-id="0fb58-189">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="0fb58-190">Добавьте и зарегистрируйте `CustomAccountFactory` в **клиентском** приложении, как показано в разделе [Настраиваемая учетная запись пользователя](#custom-user-account).</span><span class="sxs-lookup"><span data-stu-id="0fb58-190">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="0fb58-191">Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.</span><span class="sxs-lookup"><span data-stu-id="0fb58-191">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="0fb58-192">В `Program.Main` **клиентского** приложения укажите утверждение с именем `appRole` в качестве утверждения роли для проверок <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="0fb58-192">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="0fb58-193">Если вы предпочитаете использовать утверждение `directoryRoles` (роли администратора AAD), назначьте `directoryRoles` для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="0fb58-193">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0fb58-194">В `Startup.ConfigureServices` **серверного** приложения укажите утверждение с именем `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` в качестве утверждения роли для проверок <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="0fb58-194">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> <span data-ttu-id="0fb58-195">Если вы предпочитаете использовать утверждение `wids` (роли администратора AAD), назначьте `wids` для <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="0fb58-195">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="0fb58-196">На этом этапе можно применять подходы с авторизацией компонентов.</span><span class="sxs-lookup"><span data-stu-id="0fb58-196">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="0fb58-197">Любой из механизмов авторизации в компонентах **клиентского** приложения может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="0fb58-197">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="0fb58-198">Компонент `AuthorizeView`</span><span class="sxs-lookup"><span data-stu-id="0fb58-198">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="0fb58-199">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="0fb58-199">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="0fb58-200">Процедурная логика</span><span class="sxs-lookup"><span data-stu-id="0fb58-200">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="0fb58-201">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="0fb58-201">Multiple role tests are supported:</span></span>

* <span data-ttu-id="0fb58-202">Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании компонента `AuthorizeView`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-202">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="0fb58-203">Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании компонента `AuthorizeView`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-203">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="0fb58-204">Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании атрибута `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-204">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="0fb58-205">Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании атрибута `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-205">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="0fb58-206">Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании процедурного кода:</span><span class="sxs-lookup"><span data-stu-id="0fb58-206">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* <span data-ttu-id="0fb58-207">Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании процедурного кода, изменив [условное ИЛИ (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) на [условное И (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) в предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="0fb58-207">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="0fb58-208">Любой из механизмов авторизации в контроллерах **серверного** приложения может использовать роль `admin` для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="0fb58-208">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="0fb58-209">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="0fb58-209">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="0fb58-210">Процедурная логика</span><span class="sxs-lookup"><span data-stu-id="0fb58-210">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="0fb58-211">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="0fb58-211">Multiple role tests are supported:</span></span>

* <span data-ttu-id="0fb58-212">Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании атрибута `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-212">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="0fb58-213">Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании атрибута `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="0fb58-213">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="0fb58-214">Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании процедурного кода:</span><span class="sxs-lookup"><span data-stu-id="0fb58-214">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* <span data-ttu-id="0fb58-215">Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании процедурного кода, изменив [условное ИЛИ (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) на [условное И (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) в предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="0fb58-215">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="0fb58-216">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="0fb58-216">Additional resources</span></span>

* [<span data-ttu-id="0fb58-217">Идентификаторы шаблонов ролей (документация по Azure)</span><span class="sxs-lookup"><span data-stu-id="0fb58-217">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="0fb58-218">Атрибут `groupMembershipClaims` (документация по Azure)</span><span class="sxs-lookup"><span data-stu-id="0fb58-218">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="0fb58-219">Практическое руководство. Добавление ролей приложения в приложение и их получение в токене (документация по Azure)</span><span class="sxs-lookup"><span data-stu-id="0fb58-219">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="0fb58-220">Роли приложения (документация по Azure)</span><span class="sxs-lookup"><span data-stu-id="0fb58-220">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
