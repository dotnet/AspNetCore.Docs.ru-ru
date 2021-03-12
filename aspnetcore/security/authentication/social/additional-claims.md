---
title: Сохранение дополнительных утверждений и маркеров от внешних поставщиков в ASP.NET Core
author: rick-anderson
description: Узнайте, как устанавливать дополнительные утверждения и токены от внешних поставщиков.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2021
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 513de6133455e26552b79de0f07cf135e8b36825
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605572"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Сохранение дополнительных утверждений и маркеров от внешних поставщиков в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter. Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении. Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента. Для получения дополнительной информации см. <xref:security/authentication/social/index>. В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Задание идентификатора клиента и секрета клиента

Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента. Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике. Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика. Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.

* [Проверка подлинности Facebook](xref:security/authentication/facebook-logins)
* [Проверка подлинности Google](xref:security/authentication/google-logins)
* [Проверка подлинности Майкрософт](xref:security/authentication/microsoft-logins)
* [Проверка подлинности Twitter](xref:security/authentication/twitter-logins)
* [Другие поставщики проверки подлинности](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Определение области проверки подлинности

Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.

| Поставщик  | Область                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `profile`, `email`, `openid`                                     |
| пиринг Майкрософт. | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

В примере приложения `profile` платформа Google, `email` и `openid` области автоматически добавляются платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Если приложению требуются дополнительные области, добавьте их в параметры. В следующем примере `https://www.googleapis.com/auth/user.birthday.read` добавлена область Google для получения дня рождения пользователя:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Сопоставьте ключи данных пользователя и создайте утверждения

В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе. Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .

Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

В примере приложения `OnPostConfirmationAsync` (*Account/екстерналлогин. cshtml. CS*) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

По умолчанию утверждения пользователя хранятся в проверке подлинности cookie . Если проверка подлинности cookie слишком велика, это может привести к сбою приложения по следующим причинам:

* Браузер обнаруживает, что cookie заголовок слишком длинный.
* Общий размер запроса слишком велик.

Если для обработки запросов пользователей требуется большой объем пользовательских данных:

* Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.
* Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по Cookie промежуточного слоя для проверки подлинности <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах. Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.

## <a name="save-the-access-token"></a>Сохранение маркера доступа

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации. `SaveTokens` по `false` умолчанию имеет значение, чтобы уменьшить размер окончательной проверки подлинности cookie .

Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .

Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> Сведения о передаче маркеров Razor компонентам Blazor Server приложения см. в разделе <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .

## <a name="how-to-add-additional-custom-tokens"></a>Добавление дополнительных настраиваемых токенов

Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Создание и добавление утверждений

Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию. Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.

Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="add-and-update-user-claims"></a>Добавление и обновление утверждений пользователей

Утверждения копируются из внешних поставщиков в базу данных пользователя при первой регистрации, а не при входе. Если в приложении будут включены дополнительные утверждения после того, как пользователь зарегистрируется для использования приложения, вызовите [SignInManager. рефрешсигнинасинк](xref:Microsoft.AspNetCore.Identity.SignInManager%601) для пользователя, чтобы принудительно создать новую проверку подлинности cookie .

В среде разработки, работающей с тестовыми учетными записями пользователей, можно просто удалить и повторно создать учетную запись пользователя. В производственных системах новые заявки, добавленные в приложение, могут быть заполнены в учетных записях пользователей. После [формирования шаблона `ExternalLogin` страницы](xref:security/authentication/scaffold-identity) в приложении в добавьте в `Areas/Pages/Identity/Account/Manage` файл следующий код `ExternalLoginModel` `ExternalLogin.cshtml.cs` .

Добавьте словарь добавленных утверждений. Используйте ключи словаря для хранения типов утверждений и используйте значения для хранения значения по умолчанию. Добавьте следующую строку в начало класса. В следующем примере предполагается, что одно утверждение добавляется для аватара пользователя с универсальным образом хеадшот в качестве значения по умолчанию:

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

Замените код метода по умолчанию `OnGetCallbackAsync` следующим кодом. Код проходит через словарь утверждений. Утверждения добавляются (заполнены) или обновляются для каждого пользователя. При добавлении или обновлении утверждений вход пользователя обновляется с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , сохраняя существующие свойства проверки подлинности ( `AuthenticationProperties` ).

```csharp
public async Task<IActionResult> OnGetCallbackAsync(
    string returnUrl = null, string remoteError = null)
{
    returnUrl = returnUrl ?? Url.Content("~/");

    if (remoteError != null)
    {
        ErrorMessage = $"Error from external provider: {remoteError}";

        return RedirectToPage("./Login", new {ReturnUrl = returnUrl });
    }

    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        ErrorMessage = "Error loading external login information.";
        return RedirectToPage("./Login", new { ReturnUrl = returnUrl });
    }

    // Sign in the user with this external login provider if the user already has a 
    // login.
    var result = await _signInManager.ExternalLoginSignInAsync(info.LoginProvider, 
        info.ProviderKey, isPersistent: false, bypassTwoFactor : true);

    if (result.Succeeded)
    {
        _logger.LogInformation("{Name} logged in with {LoginProvider} provider.", 
            info.Principal.Identity.Name, info.LoginProvider);

        if (_claimsToSync.Count > 0)
        {
            var user = await _userManager.FindByLoginAsync(info.LoginProvider, 
                info.ProviderKey);
            var userClaims = await _userManager.GetClaimsAsync(user);
            bool refreshSignIn = false;

            foreach (var addedClaim in _claimsToSync)
            {
                var userClaim = userClaims
                    .FirstOrDefault(c => c.Type == addedClaim.Key);

                if (info.Principal.HasClaim(c => c.Type == addedClaim.Key))
                {
                    var externalClaim = info.Principal.FindFirst(addedClaim.Key);

                    if (userClaim == null)
                    {
                        await _userManager.AddClaimAsync(user, 
                            new Claim(addedClaim.Key, externalClaim.Value));
                        refreshSignIn = true;
                    }
                    else if (userClaim.Value != externalClaim.Value)
                    {
                        await _userManager
                            .ReplaceClaimAsync(user, userClaim, externalClaim);
                        refreshSignIn = true;
                    }
                }
                else if (userClaim == null)
                {
                    // Fill with a default value
                    await _userManager.AddClaimAsync(user, new Claim(addedClaim.Key, 
                        addedClaim.Value));
                    refreshSignIn = true;
                }
            }

            if (refreshSignIn)
            {
                await _signInManager.RefreshSignInAsync(user);
            }
        }

        return LocalRedirect(returnUrl);
    }

    if (result.IsLockedOut)
    {
        return RedirectToPage("./Lockout");
    }
    else
    {
        // If the user does not have an account, then ask the user to create an 
        // account.
        ReturnUrl = returnUrl;
        ProviderDisplayName = info.ProviderDisplayName;

        if (info.Principal.HasClaim(c => c.Type == ClaimTypes.Email))
        {
            Input = new InputModel
            {
                Email = info.Principal.FindFirstValue(ClaimTypes.Email)
            };
        }

        return Page();
    }
}
```

Аналогичный подход принимается при изменении утверждений, когда пользователь вошел в, но шаг обратной записи не требуется. Чтобы обновить утверждения пользователя, вызовите следующую команду для пользователя:

* [UserManager. реплацеклаимасинк](xref:Microsoft.AspNetCore.Identity.UserManager%601) для пользователя для утверждений, хранящихся в базе данных удостоверений.
* [SignInManager. рефрешсигнинасинк](xref:Microsoft.AspNetCore.Identity.SignInManager%601) для пользователя, чтобы принудительно создать новую проверку подлинности cookie .

## <a name="removal-of-claim-actions-and-claims"></a>Удаление действий утверждений и утверждений

[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции. [Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.

## <a name="sample-app-output"></a>Выходные данные примера приложения

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter. Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении. Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента. Для получения дополнительной информации см. <xref:security/authentication/social/index>. В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Задание идентификатора клиента и секрета клиента

Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента. Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике. Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика. Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.

* [Проверка подлинности Facebook](xref:security/authentication/facebook-logins)
* [Проверка подлинности Google](xref:security/authentication/google-logins)
* [Проверка подлинности Майкрософт](xref:security/authentication/microsoft-logins)
* [Проверка подлинности Twitter](xref:security/authentication/twitter-logins)
* [Другие поставщики проверки подлинности](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Определение области проверки подлинности

Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.

| Поставщик  | Область                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| пиринг Майкрософт. | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

В примере приложения `userinfo.profile` область Google автоматически добавляется платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Если приложению требуются дополнительные области, добавьте их в параметры. В следующем примере `https://www.googleapis.com/auth/user.birthday.read` область Google добавляется для получения дня рождения пользователя:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Сопоставьте ключи данных пользователя и создайте утверждения

В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе. Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .

Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

В примере приложения `OnPostConfirmationAsync` (*Account/екстерналлогин. cshtml. CS*) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

По умолчанию утверждения пользователя хранятся в проверке подлинности cookie . Если проверка подлинности cookie слишком велика, это может привести к сбою приложения по следующим причинам:

* Браузер обнаруживает, что cookie заголовок слишком длинный.
* Общий размер запроса слишком велик.

Если для обработки запросов пользователей требуется большой объем пользовательских данных:

* Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.
* Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по Cookie промежуточного слоя для проверки подлинности <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах. Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.

## <a name="save-the-access-token"></a>Сохранение маркера доступа

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации. `SaveTokens` по `false` умолчанию имеет значение, чтобы уменьшить размер окончательной проверки подлинности cookie .

Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .

Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Добавление дополнительных настраиваемых токенов

Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Создание и добавление утверждений

Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию. Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.

Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Удаление действий утверждений и утверждений

[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции. [Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.

## <a name="sample-app-output"></a>Выходные данные примера приложения

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [соЦиалсампле приложение DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): связанный пример приложения находится в технологической ветви [DotNet/AspNetCore в репозитории GitHub](https://github.com/dotnet/AspNetCore) `main` . `main`Ветвь содержит код в разделе активная разработка для следующего выпуска ASP.NET Core. Чтобы просмотреть версию примера приложения для выпущенной версии ASP.NET Core, используйте раскрывающийся список **ветвь** для выбора ветви выпуска (например, `release/{X.Y}` ).
