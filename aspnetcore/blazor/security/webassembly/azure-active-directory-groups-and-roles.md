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
ms.openlocfilehash: d1c75d85283b583d8bfd885fcd6552b69c2528c7
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758267"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Группы, роли администратора и роли приложения в Azure Active Directory (AAD)

Авторы: [Люк Латэм](https://github.com/javiercn) (Luke Latham) и [Хавьер Кальварро Нельсон](https://github.com/guardrex) (Javier Calvarro Nelson)

Azure Active Directory (AAD) предоставляет несколько подходов к авторизации, которые можно сочетать с ASP.NET Core Identity.

* Группы
  * Безопасность
  * Microsoft 365
  * Распределение
* Роли
  * Роли администратора в AAD
  * Роли приложения

Рекомендации в этой статье относятся к сценариям развертывания Blazor WebAssembly AAD, описанным в следующих разделах:

* [Автономное развертывание с помощью учетных записей Майкрософт](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Автономное развертывание с помощью AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Размещенное развертывание с помощью AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

В этой статье приведены инструкции для клиентских и серверных приложений.

* **Клиент:** отдельные приложения Blazor WebAssembly или приложение *`Client`* размещенного решения Blazor.
* **Сервер:** отдельные приложения API или веб-API сервера ASP.NET Core или приложение *`Server`* размещенного решения Blazor.

## <a name="scopes"></a>Области действия

Чтобы разрешить вызовы [API Microsoft Graph](/graph/use-the-api) для получения профиля пользователя, назначения ролей и данных о членстве в группах, **клиентское** приложение настраивается с помощью (`https://graph.microsoft.com/User.Read`) [разрешений API Graph (область)](/graph/permissions-reference) на портале Azure.

**Серверное** приложение, которое вызывает API Graph для получения данных членства в ролях и группах, настраивается с помощью [разрешения API Graph (область)](/graph/permissions-reference) `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) на портале Azure.

Эти области используются наряду с областями, требуемыми в сценариях развертывания AAD, как описано в первом разделе этой статьи.

> [!NOTE]
> Слова "разрешение" и "область" являются взаимозаменяемыми в контексте портала Azure, а также разных наборов документации Майкрософт и сторонней документации. В этой статье для всех разрешений, назначенных приложению на портале Azure, используется слово "область".

## <a name="group-membership-claims-attribute"></a>Атрибут утверждений членства в группах

В манифесте приложения на портале Azure для **клиентского** и **серверного** приложений установите для [атрибута `groupMembershipClaims`](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) значение `All`. Значение `All` позволяет получить все группы безопасности, группы рассылки и роли, членом которых является вошедший пользователь.

1. Откройте регистрацию приложения на портале Azure.
1. Выберите **Управление** > **Манифест** на панели сбоку.
1. Найдите атрибут `groupMembershipClaims`.
1. Присвойте ему значение `All`.
1. Нажмите кнопку **Сохранить**.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Настраиваемая учетная запись пользователя

Назначьте пользователей группам безопасности AAD и ролям администратора AAD на портале Azure.

Примеры в этой статье:

* Предположим, что в арендаторе AAD на портале Azure пользователю назначена роль AAD *Администратор выставления счетов* для авторизации и получения доступа к данным API сервера.
* Используйте [политики авторизации](xref:security/authorization/policies) для управления доступом в **клиентском** и **серверном** приложении.

В **клиентском** приложении обновите <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, чтобы включить свойства для:

* `Roles`: массива ролей приложения AAD (см. раздел [Роли приложения](#app-roles)).
* `Wids`: ролей администратора AAD в [утверждениях известных идентификаторов (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims).
* `Oid`: неизменяемого [утверждения идентификатора объекта (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (уникально идентифицирует пользователя в пределах и за пределами арендаторов).

Присвойте каждому свойству массива пустой массив, чтобы проверка `null` не требовалась, если эти свойства используются в циклах `foreach`.

`CustomUserAccount.cs`:

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

Добавьте ссылку на пакет в файл проекта **клиентского** приложения для [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).

Добавьте служебные классы и конфигурацию Graph SDK, описанные в разделе *Пакет SDK для Graph* статьи <xref:blazor/security/webassembly/graph-api#graph-sdk>. В классе `GraphClientExtensions` укажите область `User.Read` маркера доступа в методе `AuthenticateRequestAsync`:

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Добавьте следующую настраиваемую фабрику учетных записей пользователей в **клиентское** приложение. Эта фабрика используется для установки:

* утверждений роли приложения (`appRole`) (см. раздел [Роли приложения](#app-roles));
* утверждений роли администратора AAD (`directoryRole`);
* примера утверждения данных профиля пользователя для номера мобильного телефона пользователя (`mobilePhone`);
* утверждений группы AAD (`directoryGroup`).

`CustomAccountFactory.cs`:

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

Приведенный выше код не включает в себя транзитивные членства. Если приложению требуются прямые и транзитивные утверждения членства в группах, замените свойство `MemberOf` (`IUserMemberOfCollectionWithReferencesRequestBuilder`) на `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).

Приведенный выше код не учитывает утверждения членства в группах (`groups`), которые являются ролями администратора AAD (тип `#microsoft.graph.directoryRole`), так как значения GUID, возвращаемые платформой Identity 2.0 (Майкрософт), являются **идентификаторами сущностей** ролей администратора AAD, но не [**идентификаторами шаблонов ролей**](/azure/active-directory/roles/permissions-reference#role-template-ids). Идентификаторы сущностей не являются стабильными для арендаторов на платформе Identity 2.0 (Майкрософт) и не должны использоваться для создания политик авторизации для пользователей в приложениях. Всегда используйте **идентификаторы шаблонов ролей** для ролей администратора AAD, **предоставляемые утверждениями `wids`** .

В `Program.Main` **клиентского** приложения настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей.

`Program.cs`:

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

## <a name="authorization-configuration"></a>Настройка авторизации

В **клиентском** приложении создайте [политику](xref:security/authorization/policies) для каждой [роли приложения](#app-roles), роли администратора AAD или группы безопасности в `Program.Main`. В следующем примере создается политика для роли AAD *Администратор выставления счетов*:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Полный список идентификаторов для ролей администратора AAD см. в разделе [Идентификаторы шаблонов ролей](/azure/active-directory/roles/permissions-reference#role-template-ids) в документации Azure. Дополнительные сведения о политиках авторизации см. здесь: <xref:security/authorization/policies>.

В следующих примерах **клиентское** приложение использует предыдущую политику для авторизации пользователя.

С политикой работает [компонент`AuthorizeView`](xref:blazor/security/index#authorizeview-component):

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

Доступ ко всему компоненту может основываться на политике, использующей [директиву атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Если пользователь не вошел в систему, он перенаправляется на страницу входа AAD, а затем после входа обратно в компонент.

Проверку политики можно также [выполнять в коде с помощью процедурной логики](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

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

## <a name="authorize-server-apiweb-api-access"></a>Авторизация доступа к API и веб-API сервера

**Серверное** приложение API может авторизовать пользователей для доступа к защищенным конечным точкам API с помощью [политик авторизации](xref:security/authorization/policies) для групп безопасности, ролей администратора AAD и ролей приложений, если маркер доступа содержит утверждения `groups`, `wids` и `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`. В следующем примере создается политика для роли AAD *Администратор выставления счетов* в `Startup.ConfigureServices` с использованием утверждений `wids` (известные идентификаторы и идентификаторы шаблонов ролей):

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Полный список идентификаторов для ролей администратора AAD см. в разделе [Идентификаторы шаблонов ролей](/azure/active-directory/roles/permissions-reference#role-template-ids) в документации Azure. Дополнительные сведения о политиках авторизации см. здесь: <xref:security/authorization/policies>.

Доступ к контроллеру в **серверном** приложении может быть основан на использовании [атрибута `[Authorize]`](xref:security/authorization/simple) с именем политики (документация по API: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).

Следующий пример ограничивает доступ к данным выставления счетов с `BillingDataController` для администраторов выставления счетов Azure с использованием имени политики `BillingAdministrator`:

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

Для получения дополнительной информации см. <xref:security/authorization/policies>.

## <a name="app-roles"></a>Роли приложения

Сведения о том, как настроить приложение на портале Azure для предоставления утверждений членства ролей приложения, см. в статье [Практическое руководство. Добавление ролей приложения в приложение, зарегистрированное в Azure Active Directory, и их получение в токене](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) документации Azure.

В следующем примере предполагается, что **клиентское** и **серверное** приложения настроены с помощью двух ролей, а роли назначены тестовому пользователю:

* `admin`
* `developer`

> [!NOTE]
> При разработке размещенного приложения Blazor WebAssembly или пары отдельных приложений "клиент — сервер" (отдельное приложение Blazor WebAssembly и отдельное приложение API или веб-API сервера ASP.NET Core), свойство манифеста `appRoles` регистраций клиентского и серверного приложений на портале Azure должно включать одни и те же настроенные роли. Установив роли в манифесте клиентского приложения, скопируйте их целиком в манифест серверного приложения. Если не создать зеркальную копию манифеста `appRoles` между регистрацией клиентских и серверных приложений, утверждения ролей не будут установлены для пользователей API или веб-API сервера, прошедших проверку подлинности, даже если их маркер доступа имеет правильные утверждения ролей.

> [!NOTE]
> Хотя вы не можете назначать роли группам без учетной записи Azure AD Premium, вы можете назначить роли пользователям и получить утверждение ролей для пользователей с помощью стандартной учетной записи Azure. В рекомендациях в этом разделе не предполагается использовать учетную запись AAD Premium.
>
> На портале Azure назначается несколько ролей путем **_повторного добавления пользователей_** для каждого дополнительного назначения роли.

`CustomAccountFactory`, как показано в разделе [Настраиваемая учетная запись пользователя](#custom-user-account), настраивается для работы в утверждении `roles` с использованием значения массива JSON. Добавьте и зарегистрируйте `CustomAccountFactory` в **клиентском** приложении, как показано в разделе [Настраиваемая учетная запись пользователя](#custom-user-account). Нет необходимости предоставлять код для удаления исходного утверждения `roles`, поскольку оно автоматически удаляется платформой.

В `Program.Main` **клиентского** приложения укажите утверждение с именем `appRole` в качестве утверждения роли для проверок <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> Если вы предпочитаете использовать утверждение `directoryRoles` (роли администратора AAD), назначьте `directoryRoles` для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.

В `Startup.ConfigureServices` **серверного** приложения укажите утверждение с именем `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` в качестве утверждения роли для проверок <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>:

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
> Если вы предпочитаете использовать утверждение `wids` (роли администратора AAD), назначьте `wids` для <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.

На этом этапе можно применять подходы с авторизацией компонентов. Любой из механизмов авторизации в компонентах **клиентского** приложения может использовать роль `admin` для авторизации пользователя:

* [Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Процедурная логика](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Поддерживается тестирование с использованием нескольких ролей:

* Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании компонента `AuthorizeView`:

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании компонента `AuthorizeView`:

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании атрибута `[Authorize]`:

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании атрибута `[Authorize]`:

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании процедурного кода:

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

* Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании процедурного кода, изменив [условное ИЛИ (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) на [условное И (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) в предыдущем примере:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

Любой из механизмов авторизации в контроллерах **серверного** приложения может использовать роль `admin` для авторизации пользователя:

* [Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Процедурная логика](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Поддерживается тестирование с использованием нескольких ролей:

* Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании атрибута `[Authorize]`:

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании атрибута `[Authorize]`:

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Требовать, чтобы пользователь имел **одну из** ролей: `admin` **или** `developer` при использовании процедурного кода:

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

* Требовать, чтобы пользователь имел **обе** роли: `admin` **и** `developer` при использовании процедурного кода, изменив [условное ИЛИ (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) на [условное И (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) в предыдущем примере:

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Идентификаторы шаблонов ролей (документация по Azure)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [Атрибут `groupMembershipClaims` (документация по Azure)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [Практическое руководство. Добавление ролей приложения в приложение и их получение в токене (документация по Azure)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Роли приложения (документация по Azure)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
