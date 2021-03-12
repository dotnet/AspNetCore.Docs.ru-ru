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
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="ce048-103">Сохранение дополнительных утверждений и маркеров от внешних поставщиков в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce048-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce048-104">Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter.</span><span class="sxs-lookup"><span data-stu-id="ce048-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ce048-105">Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.</span><span class="sxs-lookup"><span data-stu-id="ce048-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ce048-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce048-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ce048-107">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ce048-107">Prerequisites</span></span>

<span data-ttu-id="ce048-108">Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении.</span><span class="sxs-lookup"><span data-stu-id="ce048-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ce048-109">Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="ce048-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ce048-110">Для получения дополнительной информации см. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="ce048-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ce048-111">В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="ce048-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ce048-112">Задание идентификатора клиента и секрета клиента</span><span class="sxs-lookup"><span data-stu-id="ce048-112">Set the client ID and client secret</span></span>

<span data-ttu-id="ce048-113">Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="ce048-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ce048-114">Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике.</span><span class="sxs-lookup"><span data-stu-id="ce048-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ce048-115">Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика.</span><span class="sxs-lookup"><span data-stu-id="ce048-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ce048-116">Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.</span><span class="sxs-lookup"><span data-stu-id="ce048-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ce048-117">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="ce048-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ce048-118">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="ce048-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ce048-119">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="ce048-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ce048-120">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="ce048-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ce048-121">Другие поставщики проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ce048-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ce048-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ce048-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ce048-123">Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:</span><span class="sxs-lookup"><span data-stu-id="ce048-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ce048-124">Определение области проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ce048-124">Establish the authentication scope</span></span>

<span data-ttu-id="ce048-125">Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="ce048-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ce048-126">В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="ce048-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ce048-127">Поставщик</span><span class="sxs-lookup"><span data-stu-id="ce048-127">Provider</span></span>  | <span data-ttu-id="ce048-128">Область</span><span class="sxs-lookup"><span data-stu-id="ce048-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ce048-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="ce048-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ce048-130">Google</span><span class="sxs-lookup"><span data-stu-id="ce048-130">Google</span></span>    | <span data-ttu-id="ce048-131">`profile`, `email`, `openid`</span><span class="sxs-lookup"><span data-stu-id="ce048-131">`profile`, `email`, `openid`</span></span>                                     |
| <span data-ttu-id="ce048-132">пиринг Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="ce048-132">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ce048-133">Twitter</span><span class="sxs-lookup"><span data-stu-id="ce048-133">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ce048-134">В примере приложения `profile` платформа Google, `email` и `openid` области автоматически добавляются платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ce048-134">In the sample app, Google's `profile`, `email`, and `openid` scopes are automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle%2A> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ce048-135">Если приложению требуются дополнительные области, добавьте их в параметры.</span><span class="sxs-lookup"><span data-stu-id="ce048-135">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ce048-136">В следующем примере `https://www.googleapis.com/auth/user.birthday.read` добавлена область Google для получения дня рождения пользователя:</span><span class="sxs-lookup"><span data-stu-id="ce048-136">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ce048-137">Сопоставьте ключи данных пользователя и создайте утверждения</span><span class="sxs-lookup"><span data-stu-id="ce048-137">Map user data keys and create claims</span></span>

<span data-ttu-id="ce048-138">В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе.</span><span class="sxs-lookup"><span data-stu-id="ce048-138">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ce048-139">Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="ce048-139">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ce048-140">Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:</span><span class="sxs-lookup"><span data-stu-id="ce048-140">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ce048-141">В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ce048-141">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ce048-142">Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="ce048-142">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ce048-143">В примере приложения `OnPostConfirmationAsync` (*Account/екстерналлогин. cshtml. CS*) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="ce048-143">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ce048-144">По умолчанию утверждения пользователя хранятся в проверке подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="ce048-144">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ce048-145">Если проверка подлинности cookie слишком велика, это может привести к сбою приложения по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="ce048-145">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ce048-146">Браузер обнаруживает, что cookie заголовок слишком длинный.</span><span class="sxs-lookup"><span data-stu-id="ce048-146">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ce048-147">Общий размер запроса слишком велик.</span><span class="sxs-lookup"><span data-stu-id="ce048-147">The overall size of the request is too large.</span></span>

<span data-ttu-id="ce048-148">Если для обработки запросов пользователей требуется большой объем пользовательских данных:</span><span class="sxs-lookup"><span data-stu-id="ce048-148">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ce048-149">Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="ce048-149">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ce048-150">Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по Cookie промежуточного слоя для проверки подлинности <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах.</span><span class="sxs-lookup"><span data-stu-id="ce048-150">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ce048-151">Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.</span><span class="sxs-lookup"><span data-stu-id="ce048-151">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ce048-152">Сохранение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="ce048-152">Save the access token</span></span>

<span data-ttu-id="ce048-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="ce048-153"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ce048-154">`SaveTokens` по `false` умолчанию имеет значение, чтобы уменьшить размер окончательной проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="ce048-154">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ce048-155">Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="ce048-155">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ce048-156">Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="ce048-156">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ce048-157">Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="ce048-157">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

> [!NOTE]
> <span data-ttu-id="ce048-158">Сведения о передаче маркеров Razor компонентам Blazor Server приложения см. в разделе <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app> .</span><span class="sxs-lookup"><span data-stu-id="ce048-158">For information on passing tokens to the Razor components of a Blazor Server app, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ce048-159">Добавление дополнительных настраиваемых токенов</span><span class="sxs-lookup"><span data-stu-id="ce048-159">How to add additional custom tokens</span></span>

<span data-ttu-id="ce048-160">Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="ce048-160">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ce048-161">Создание и добавление утверждений</span><span class="sxs-lookup"><span data-stu-id="ce048-161">Creating and adding claims</span></span>

<span data-ttu-id="ce048-162">Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию.</span><span class="sxs-lookup"><span data-stu-id="ce048-162">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ce048-163">Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="ce048-163">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ce048-164">Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.</span><span class="sxs-lookup"><span data-stu-id="ce048-164">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ce048-165">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="ce048-165">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="add-and-update-user-claims"></a><span data-ttu-id="ce048-166">Добавление и обновление утверждений пользователей</span><span class="sxs-lookup"><span data-stu-id="ce048-166">Add and update user claims</span></span>

<span data-ttu-id="ce048-167">Утверждения копируются из внешних поставщиков в базу данных пользователя при первой регистрации, а не при входе.</span><span class="sxs-lookup"><span data-stu-id="ce048-167">Claims are copied from external providers to the user database on first registration, not on sign in.</span></span> <span data-ttu-id="ce048-168">Если в приложении будут включены дополнительные утверждения после того, как пользователь зарегистрируется для использования приложения, вызовите [SignInManager. рефрешсигнинасинк](xref:Microsoft.AspNetCore.Identity.SignInManager%601) для пользователя, чтобы принудительно создать новую проверку подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="ce048-168">If additional claims are enabled in an app after a user registers to use the app, call [SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on a user to force the generation of a new authentication cookie.</span></span>

<span data-ttu-id="ce048-169">В среде разработки, работающей с тестовыми учетными записями пользователей, можно просто удалить и повторно создать учетную запись пользователя.</span><span class="sxs-lookup"><span data-stu-id="ce048-169">In the Development environment working with test user accounts, you can simply delete and recreate the user account.</span></span> <span data-ttu-id="ce048-170">В производственных системах новые заявки, добавленные в приложение, могут быть заполнены в учетных записях пользователей.</span><span class="sxs-lookup"><span data-stu-id="ce048-170">For production systems, new claims added to the app can be backfilled into user accounts.</span></span> <span data-ttu-id="ce048-171">После [формирования шаблона `ExternalLogin` страницы](xref:security/authentication/scaffold-identity) в приложении в добавьте в `Areas/Pages/Identity/Account/Manage` файл следующий код `ExternalLoginModel` `ExternalLogin.cshtml.cs` .</span><span class="sxs-lookup"><span data-stu-id="ce048-171">After [scaffolding the `ExternalLogin` page](xref:security/authentication/scaffold-identity) into the app at `Areas/Pages/Identity/Account/Manage`, add the following code to the `ExternalLoginModel` in the `ExternalLogin.cshtml.cs` file.</span></span>

<span data-ttu-id="ce048-172">Добавьте словарь добавленных утверждений.</span><span class="sxs-lookup"><span data-stu-id="ce048-172">Add a dictionary of added claims.</span></span> <span data-ttu-id="ce048-173">Используйте ключи словаря для хранения типов утверждений и используйте значения для хранения значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="ce048-173">Use the dictionary keys to hold the claim types, and use the values to hold a default value.</span></span> <span data-ttu-id="ce048-174">Добавьте следующую строку в начало класса.</span><span class="sxs-lookup"><span data-stu-id="ce048-174">Add the following line to the top of the class.</span></span> <span data-ttu-id="ce048-175">В следующем примере предполагается, что одно утверждение добавляется для аватара пользователя с универсальным образом хеадшот в качестве значения по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="ce048-175">The following example assumes that one claim is added for the user's Google picture with a generic headshot image as the default value:</span></span>

```csharp
private readonly IReadOnlyDictionary<string, string> _claimsToSync = 
    new Dictionary<string, string>()
    {
        { "urn:google:picture", "https://localhost:5001/headshot.png" },
    };
```

<span data-ttu-id="ce048-176">Замените код метода по умолчанию `OnGetCallbackAsync` следующим кодом.</span><span class="sxs-lookup"><span data-stu-id="ce048-176">Replace the default code of the `OnGetCallbackAsync` method with the following code.</span></span> <span data-ttu-id="ce048-177">Код проходит через словарь утверждений.</span><span class="sxs-lookup"><span data-stu-id="ce048-177">The code loops through the claims dictionary.</span></span> <span data-ttu-id="ce048-178">Утверждения добавляются (заполнены) или обновляются для каждого пользователя.</span><span class="sxs-lookup"><span data-stu-id="ce048-178">Claims are added (backfilled) or updated for each user.</span></span> <span data-ttu-id="ce048-179">При добавлении или обновлении утверждений вход пользователя обновляется с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601> , сохраняя существующие свойства проверки подлинности ( `AuthenticationProperties` ).</span><span class="sxs-lookup"><span data-stu-id="ce048-179">When claims are added or updated, the user sign-in is refreshed using the <xref:Microsoft.AspNetCore.Identity.SignInManager%601>, preserving the existing authentication properties (`AuthenticationProperties`).</span></span>

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

<span data-ttu-id="ce048-180">Аналогичный подход принимается при изменении утверждений, когда пользователь вошел в, но шаг обратной записи не требуется.</span><span class="sxs-lookup"><span data-stu-id="ce048-180">A similar approach is taken when claims change while a user is signed in but a backfill step isn't required.</span></span> <span data-ttu-id="ce048-181">Чтобы обновить утверждения пользователя, вызовите следующую команду для пользователя:</span><span class="sxs-lookup"><span data-stu-id="ce048-181">To update a user's claims, call the following on the user:</span></span>

* <span data-ttu-id="ce048-182">[UserManager. реплацеклаимасинк](xref:Microsoft.AspNetCore.Identity.UserManager%601) для пользователя для утверждений, хранящихся в базе данных удостоверений.</span><span class="sxs-lookup"><span data-stu-id="ce048-182">[UserManager.ReplaceClaimAsync](xref:Microsoft.AspNetCore.Identity.UserManager%601) on the user for claims stored in the identity database.</span></span>
* <span data-ttu-id="ce048-183">[SignInManager. рефрешсигнинасинк](xref:Microsoft.AspNetCore.Identity.SignInManager%601) для пользователя, чтобы принудительно создать новую проверку подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="ce048-183">[SignInManager.RefreshSignInAsync](xref:Microsoft.AspNetCore.Identity.SignInManager%601) on the user to force the generation of a new authentication cookie.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ce048-184">Удаление действий утверждений и утверждений</span><span class="sxs-lookup"><span data-stu-id="ce048-184">Removal of claim actions and claims</span></span>

<span data-ttu-id="ce048-185">[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции.</span><span class="sxs-lookup"><span data-stu-id="ce048-185">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ce048-186">[Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения.</span><span class="sxs-lookup"><span data-stu-id="ce048-186">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ce048-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.</span><span class="sxs-lookup"><span data-stu-id="ce048-187"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ce048-188">Выходные данные примера приложения</span><span class="sxs-lookup"><span data-stu-id="ce048-188">Sample app output</span></span>

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

<span data-ttu-id="ce048-189">Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter.</span><span class="sxs-lookup"><span data-stu-id="ce048-189">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="ce048-190">Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.</span><span class="sxs-lookup"><span data-stu-id="ce048-190">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="ce048-191">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce048-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ce048-192">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="ce048-192">Prerequisites</span></span>

<span data-ttu-id="ce048-193">Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении.</span><span class="sxs-lookup"><span data-stu-id="ce048-193">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="ce048-194">Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="ce048-194">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="ce048-195">Для получения дополнительной информации см. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="ce048-195">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="ce048-196">В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="ce048-196">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="ce048-197">Задание идентификатора клиента и секрета клиента</span><span class="sxs-lookup"><span data-stu-id="ce048-197">Set the client ID and client secret</span></span>

<span data-ttu-id="ce048-198">Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="ce048-198">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="ce048-199">Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике.</span><span class="sxs-lookup"><span data-stu-id="ce048-199">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="ce048-200">Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика.</span><span class="sxs-lookup"><span data-stu-id="ce048-200">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="ce048-201">Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.</span><span class="sxs-lookup"><span data-stu-id="ce048-201">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="ce048-202">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="ce048-202">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ce048-203">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="ce048-203">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ce048-204">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="ce048-204">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="ce048-205">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="ce048-205">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ce048-206">Другие поставщики проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ce048-206">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="ce048-207">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="ce048-207">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="ce048-208">Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:</span><span class="sxs-lookup"><span data-stu-id="ce048-208">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="ce048-209">Определение области проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ce048-209">Establish the authentication scope</span></span>

<span data-ttu-id="ce048-210">Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="ce048-210">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="ce048-211">В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="ce048-211">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="ce048-212">Поставщик</span><span class="sxs-lookup"><span data-stu-id="ce048-212">Provider</span></span>  | <span data-ttu-id="ce048-213">Область</span><span class="sxs-lookup"><span data-stu-id="ce048-213">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="ce048-214">Facebook</span><span class="sxs-lookup"><span data-stu-id="ce048-214">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="ce048-215">Google</span><span class="sxs-lookup"><span data-stu-id="ce048-215">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="ce048-216">пиринг Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="ce048-216">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="ce048-217">Twitter</span><span class="sxs-lookup"><span data-stu-id="ce048-217">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="ce048-218">В примере приложения `userinfo.profile` область Google автоматически добавляется платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ce048-218">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="ce048-219">Если приложению требуются дополнительные области, добавьте их в параметры.</span><span class="sxs-lookup"><span data-stu-id="ce048-219">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="ce048-220">В следующем примере `https://www.googleapis.com/auth/user.birthday.read` область Google добавляется для получения дня рождения пользователя:</span><span class="sxs-lookup"><span data-stu-id="ce048-220">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="ce048-221">Сопоставьте ключи данных пользователя и создайте утверждения</span><span class="sxs-lookup"><span data-stu-id="ce048-221">Map user data keys and create claims</span></span>

<span data-ttu-id="ce048-222">В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе.</span><span class="sxs-lookup"><span data-stu-id="ce048-222">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="ce048-223">Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="ce048-223">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="ce048-224">Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:</span><span class="sxs-lookup"><span data-stu-id="ce048-224">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="ce048-225">В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ce048-225">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="ce048-226">Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="ce048-226">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="ce048-227">В примере приложения `OnPostConfirmationAsync` (*Account/екстерналлогин. cshtml. CS*) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="ce048-227">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="ce048-228">По умолчанию утверждения пользователя хранятся в проверке подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="ce048-228">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="ce048-229">Если проверка подлинности cookie слишком велика, это может привести к сбою приложения по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="ce048-229">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="ce048-230">Браузер обнаруживает, что cookie заголовок слишком длинный.</span><span class="sxs-lookup"><span data-stu-id="ce048-230">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="ce048-231">Общий размер запроса слишком велик.</span><span class="sxs-lookup"><span data-stu-id="ce048-231">The overall size of the request is too large.</span></span>

<span data-ttu-id="ce048-232">Если для обработки запросов пользователей требуется большой объем пользовательских данных:</span><span class="sxs-lookup"><span data-stu-id="ce048-232">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="ce048-233">Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="ce048-233">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="ce048-234">Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по Cookie промежуточного слоя для проверки подлинности <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах.</span><span class="sxs-lookup"><span data-stu-id="ce048-234">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="ce048-235">Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.</span><span class="sxs-lookup"><span data-stu-id="ce048-235">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="ce048-236">Сохранение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="ce048-236">Save the access token</span></span>

<span data-ttu-id="ce048-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="ce048-237"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="ce048-238">`SaveTokens` по `false` умолчанию имеет значение, чтобы уменьшить размер окончательной проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="ce048-238">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="ce048-239">Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="ce048-239">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="ce048-240">Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="ce048-240">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="ce048-241">Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="ce048-241">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="ce048-242">Добавление дополнительных настраиваемых токенов</span><span class="sxs-lookup"><span data-stu-id="ce048-242">How to add additional custom tokens</span></span>

<span data-ttu-id="ce048-243">Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="ce048-243">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="ce048-244">Создание и добавление утверждений</span><span class="sxs-lookup"><span data-stu-id="ce048-244">Creating and adding claims</span></span>

<span data-ttu-id="ce048-245">Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию.</span><span class="sxs-lookup"><span data-stu-id="ce048-245">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="ce048-246">Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="ce048-246">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="ce048-247">Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.</span><span class="sxs-lookup"><span data-stu-id="ce048-247">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="ce048-248">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="ce048-248">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="ce048-249">Удаление действий утверждений и утверждений</span><span class="sxs-lookup"><span data-stu-id="ce048-249">Removal of claim actions and claims</span></span>

<span data-ttu-id="ce048-250">[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции.</span><span class="sxs-lookup"><span data-stu-id="ce048-250">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="ce048-251">[Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения.</span><span class="sxs-lookup"><span data-stu-id="ce048-251">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="ce048-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.</span><span class="sxs-lookup"><span data-stu-id="ce048-252"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="ce048-253">Выходные данные примера приложения</span><span class="sxs-lookup"><span data-stu-id="ce048-253">Sample app output</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="ce048-254">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ce048-254">Additional resources</span></span>

* <span data-ttu-id="ce048-255">[соЦиалсампле приложение DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): связанный пример приложения находится в технологической ветви [DotNet/AspNetCore в репозитории GitHub](https://github.com/dotnet/AspNetCore) `main` .</span><span class="sxs-lookup"><span data-stu-id="ce048-255">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/main/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `main` engineering branch.</span></span> <span data-ttu-id="ce048-256">`main`Ветвь содержит код в разделе активная разработка для следующего выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ce048-256">The `main` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="ce048-257">Чтобы просмотреть версию примера приложения для выпущенной версии ASP.NET Core, используйте раскрывающийся список **ветвь** для выбора ветви выпуска (например, `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="ce048-257">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
