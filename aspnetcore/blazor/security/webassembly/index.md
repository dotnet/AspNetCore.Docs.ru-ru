---
title: Защита ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте о защите приложений Blazor WebAssemlby как одностраничных приложений (SPA).
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: c4d1095d3c3b457ca4d94cb1a573c3dc49548ff3
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024643"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="2aaea-103">Защита ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2aaea-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="2aaea-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="2aaea-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="2aaea-105">Защита приложений Blazor WebAssembly обеспечивается аналогично защите одностраничных приложений (SPA).</span><span class="sxs-lookup"><span data-stu-id="2aaea-105">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="2aaea-106">Существует несколько подходов к проверке подлинности пользователей в одностраничных приложениях, но наиболее распространенным и комплексным является использование реализации на основе [протокола OAuth 2.0](https://oauth.net/), например, [OpenID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="2aaea-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="2aaea-107">Библиотека проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2aaea-107">Authentication library</span></span>

<span data-ttu-id="2aaea-108">Blazor WebAssembly поддерживает проверку подлинности и авторизацию приложений с использованием OIDC и библиотеки [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="2aaea-108">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="2aaea-109">Библиотека предоставляет набор примитивов для простой проверки подлинности на серверных компонентах ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2aaea-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="2aaea-110">Библиотека включает в себя ASP.NET Core Identity с поддержкой авторизации API на основе [сервера Identity](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="2aaea-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="2aaea-111">Библиотеку можно использовать для проверки подлинности любого стороннего поставщика Identity (IP), который поддерживает OIDC (их называют поставщиками OpenID (OP)).</span><span class="sxs-lookup"><span data-stu-id="2aaea-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="2aaea-112">Проверка подлинности в Blazor WebAssembly реализуется с помощью библиотеки `oidc-client.js`, которая используется для управления сведениями о базовом протоколе проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-112">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="2aaea-113">Кроме того, доступны другие варианты проверки подлинности одностраничных приложений, например использование файлов cookie SameSite.</span><span class="sxs-lookup"><span data-stu-id="2aaea-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="2aaea-114">Однако технический проект Blazor WebAssembly реализован на основе OAuth и OIDC и представляет собой наилучший вариант для проверки подлинности в приложениях Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="2aaea-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="2aaea-115">По соображениям безопасности и в силу функциональных причин, [проверка подлинности на основе токенов](xref:security/anti-request-forgery#token-based-authentication), на базе [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html), была выбрана вместо [проверки подлинности на основе файлов cookie](xref:security/anti-request-forgery#cookie-based-authentication).</span><span class="sxs-lookup"><span data-stu-id="2aaea-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="2aaea-116">Использование протокола на основе токенов позволяет уменьшить контактную зону атак, так как токены отправляются не во всех запросах.</span><span class="sxs-lookup"><span data-stu-id="2aaea-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="2aaea-117">Конечные точки сервера не нуждаются в защите от [подделки межсайтовых запросов (CSRF)](xref:security/anti-request-forgery), так как токены отправляются явным образом.</span><span class="sxs-lookup"><span data-stu-id="2aaea-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="2aaea-118">Это позволяет размещать приложения Blazor WebAssembly параллельно с приложениями MVC или Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2aaea-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="2aaea-119">Разрешения токенов более узкие, чем разрешения файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="2aaea-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="2aaea-120">Например, токены нельзя использовать для управления учетной записью пользователя или изменения пароля пользователя, если такие функции не реализованы явным образом.</span><span class="sxs-lookup"><span data-stu-id="2aaea-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="2aaea-121">У токенов короткий срок жизни (по умолчанию — один час), что ограничивает распространение атаки.</span><span class="sxs-lookup"><span data-stu-id="2aaea-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="2aaea-122">Токены можно отозвать в любое время.</span><span class="sxs-lookup"><span data-stu-id="2aaea-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="2aaea-123">Автономные токены JWT гарантируют выполнение надлежащего процесса проверки подлинности на клиенте и сервере.</span><span class="sxs-lookup"><span data-stu-id="2aaea-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="2aaea-124">Например, на клиенте есть средства для обнаружения и проверки допустимости получаемых токенов, а также подтверждения их выпуска в рамках данного процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="2aaea-125">Если третья сторона пытается изменить токен в ходе процесса проверки подлинности, клиент может обнаружить измененный токен и не использовать его.</span><span class="sxs-lookup"><span data-stu-id="2aaea-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="2aaea-126">Токены с OAuth и OIDC обеспечивают безопасность приложения вне зависимости от поведения агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="2aaea-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="2aaea-127">Протоколы на основе токенов, такие как OAuth и OIDC, позволяют выполнять проверку подлинности и авторизацию размещенных и автономных приложений с одинаковым набором характеристик безопасности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="2aaea-128">Процесс проверки подлинности с использованием OIDC</span><span class="sxs-lookup"><span data-stu-id="2aaea-128">Authentication process with OIDC</span></span>

<span data-ttu-id="2aaea-129">Библиотека [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) предлагает несколько примитивов для реализации проверки подлинности и авторизации с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="2aaea-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="2aaea-130">В общих чертах проверка подлинности работает следующим образом.</span><span class="sxs-lookup"><span data-stu-id="2aaea-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="2aaea-131">Когда анонимный пользователь нажимает кнопку входа или запрашивает страницу с примененным атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), он перенаправляется на страницу входа приложения (`/authentication/login`).</span><span class="sxs-lookup"><span data-stu-id="2aaea-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="2aaea-132">На странице входа библиотека проверки подлинности готовится к перенаправлению на конечную точку авторизации.</span><span class="sxs-lookup"><span data-stu-id="2aaea-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="2aaea-133">Конечная точка авторизации находится вне приложения Blazor WebAssembly и может размещаться в отдельном источнике.</span><span class="sxs-lookup"><span data-stu-id="2aaea-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="2aaea-134">Конечная точка определяет, прошел ли пользователь проверку подлинности, и выдает в ответ один или несколько токенов.</span><span class="sxs-lookup"><span data-stu-id="2aaea-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="2aaea-135">Библиотека проверки подлинности предоставляет обратный вызов входа для получения ответа проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="2aaea-136">Если пользователь не прошел аутентификацию, он перенаправляется в базовую систему аутентификации. Обычно это ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="2aaea-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="2aaea-137">Если пользователь уже прошел проверку подлинности, конечная точка авторизации создает соответствующие токены и перенаправляет браузер назад в конечную точку обратного вызова входа (`/authentication/login-callback`).</span><span class="sxs-lookup"><span data-stu-id="2aaea-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="2aaea-138">Когда приложение Blazor WebAssembly загружает конечную точку обратного вызова входа (`/authentication/login-callback`), происходит обработка ответа проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="2aaea-139">Если процесс проверки подлинности завершается успешно, пользователь проходит проверку подлинности и при необходимости перенаправляется по запрошенному исходному защищенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="2aaea-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="2aaea-140">Если по какой-либо причине проверка подлинности завершается сбоем, пользователь направляется на страницу ошибки входа (`/authentication/login-failed`), и выводится сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="2aaea-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="2aaea-141">`Authentication`</span><span class="sxs-lookup"><span data-stu-id="2aaea-141">`Authentication` component</span></span>

<span data-ttu-id="2aaea-142">Компонент `Authentication` (`Pages/Authentication.razor`) обрабатывает операции удаленной проверки подлинности и позволяет приложению выполнять следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2aaea-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="2aaea-143">настраивать маршруты приложения для различных состояний проверки подлинности;</span><span class="sxs-lookup"><span data-stu-id="2aaea-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="2aaea-144">задавать содержимое пользовательского интерфейса для различных состояний проверки подлинности;</span><span class="sxs-lookup"><span data-stu-id="2aaea-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="2aaea-145">управлять состоянием проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-145">Manage authentication state.</span></span>

<span data-ttu-id="2aaea-146">Действия по проверке подлинности, такие как регистрация или вход пользователя в систему, передаются в компонент <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> платформы Blazor, который сохраняет и контролирует состояние в операциях проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2aaea-146">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="2aaea-147">Дополнительные сведения и примеры см. в разделе <xref:blazor/security/webassembly/additional-scenarios>.</span><span class="sxs-lookup"><span data-stu-id="2aaea-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="2aaea-148">Авторизация</span><span class="sxs-lookup"><span data-stu-id="2aaea-148">Authorization</span></span>

<span data-ttu-id="2aaea-149">В приложениях Blazor WebAssembly авторизацию можно обойти, так как пользователь может изменять весь код на стороне клиента.</span><span class="sxs-lookup"><span data-stu-id="2aaea-149">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="2aaea-150">Это же справедливо для всех технологий на стороне клиента, включая платформы одностраничного приложения JavaScript или собственных приложений для любой операционной системы.</span><span class="sxs-lookup"><span data-stu-id="2aaea-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="2aaea-151">**Всегда выполняйте проверки авторизации на стороне сервера в конечных точках API, к которым обращается клиентское приложение.**</span><span class="sxs-lookup"><span data-stu-id="2aaea-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="2aaea-152">Требование авторизации для всего приложения</span><span class="sxs-lookup"><span data-stu-id="2aaea-152">Require authorization for the entire app</span></span>

<span data-ttu-id="2aaea-153">Примените [атрибут `[Authorize]`](xref:blazor/security/index#authorize-attribute) ([документация по API](xref:System.Web.Mvc.AuthorizeAttribute)) к каждому компоненту Razor приложения, используя один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="2aaea-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="2aaea-154">Используйте директиву [`@attribute`](xref:mvc/views/razor#attribute) в файле `_Imports.razor`:</span><span class="sxs-lookup"><span data-stu-id="2aaea-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="2aaea-155">Добавьте атрибут к каждому компоненту Razor в папке `Pages`.</span><span class="sxs-lookup"><span data-stu-id="2aaea-155">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="2aaea-156">Задание политики <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> с использованием <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> **не** поддерживается.</span><span class="sxs-lookup"><span data-stu-id="2aaea-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="2aaea-157">Маркеры обновления</span><span class="sxs-lookup"><span data-stu-id="2aaea-157">Refresh tokens</span></span>

<span data-ttu-id="2aaea-158">Маркеры обновления не могут быть защищены на стороне клиента в приложениях Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="2aaea-158">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="2aaea-159">Поэтому маркеры обновления не должны отправляться в приложение для непосредственного использования.</span><span class="sxs-lookup"><span data-stu-id="2aaea-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="2aaea-160">Маркеры обновления могут поддерживаться и использоваться серверным приложением в размещенном решении Blazor WebAssembly для доступа к интерфейсам API сторонних производителей.</span><span class="sxs-lookup"><span data-stu-id="2aaea-160">Refresh tokens can be maintained and used by the server-side app in a Hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="2aaea-161">Для получения дополнительной информации см. <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span><span class="sxs-lookup"><span data-stu-id="2aaea-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="2aaea-162">Создание утверждений для пользователей</span><span class="sxs-lookup"><span data-stu-id="2aaea-162">Establish claims for users</span></span>

<span data-ttu-id="2aaea-163">Приложения часто требуют утверждений для пользователей при вызове сервера в веб-API.</span><span class="sxs-lookup"><span data-stu-id="2aaea-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="2aaea-164">Например, утверждения часто используются в приложении для [получения авторизации](xref:blazor/security/index#authorization).</span><span class="sxs-lookup"><span data-stu-id="2aaea-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="2aaea-165">В этих сценариях приложение запрашивает маркер доступа к службе и получает с его помощью пользовательские данные для утверждений.</span><span class="sxs-lookup"><span data-stu-id="2aaea-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="2aaea-166">Для примера см. следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="2aaea-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="2aaea-167">Дополнительные сценарии. Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="2aaea-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a><span data-ttu-id="2aaea-168">Рекомендации по реализации</span><span class="sxs-lookup"><span data-stu-id="2aaea-168">Implementation guidance</span></span>

<span data-ttu-id="2aaea-169">Статьи в этом *обзоре* содержат сведения о проверке подлинности пользователей в приложениях Blazor WebAssembly для конкретных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="2aaea-169">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="2aaea-170">Автономные приложения Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="2aaea-170">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="2aaea-171">Общие рекомендации по использованию поставщиков OIDC и библиотеки проверки подлинности WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2aaea-171">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="2aaea-172">Учетные записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="2aaea-172">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="2aaea-173">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="2aaea-173">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="2aaea-174">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="2aaea-174">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="2aaea-175">Размещенные приложения Blazor WebAssembly:</span><span class="sxs-lookup"><span data-stu-id="2aaea-175">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="2aaea-176">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="2aaea-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="2aaea-177">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="2aaea-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="2aaea-178">Сервер Identity</span><span class="sxs-lookup"><span data-stu-id="2aaea-178">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2aaea-179">Дополнительное руководство по конфигурации см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="2aaea-179">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2aaea-180">Дополнительное руководство по настройке: <xref:blazor/security/webassembly/additional-scenarios>.</span><span class="sxs-lookup"><span data-stu-id="2aaea-180">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2aaea-181">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2aaea-181">Additional resources</span></span>

* <span data-ttu-id="2aaea-182"><xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:</span><span class="sxs-lookup"><span data-stu-id="2aaea-182"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="2aaea-183">Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.</span><span class="sxs-lookup"><span data-stu-id="2aaea-183">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="2aaea-184">Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.</span><span class="sxs-lookup"><span data-stu-id="2aaea-184">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
