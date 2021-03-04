---
title: Работа с SameSite cookie s в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать SameSite cookie s в ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
- Electron
uid: security/samesite
ms.openlocfilehash: e06c73edfc999053e0aa37f05d984a2b428f69a9
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109940"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="54b59-103">Работа с SameSite cookie s в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54b59-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="54b59-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="54b59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="54b59-105">SameSite — это стандартный черновик [IETF](https://ietf.org/about/) , предназначенный для обеспечения защиты от атак с подделкой межсайтовых запросов (CSRF).</span><span class="sxs-lookup"><span data-stu-id="54b59-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="54b59-106">Черновик, первоначально измененный в [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), был обновлен в [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="54b59-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="54b59-107">Обновленный стандарт не обладает обратной совместимостью с предыдущим стандартом, и ниже приведены наиболее заметные отличия.</span><span class="sxs-lookup"><span data-stu-id="54b59-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="54b59-108">Cookieпо умолчанию обрабатываются без заголовка SameSite `SameSite=Lax` .</span><span class="sxs-lookup"><span data-stu-id="54b59-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="54b59-109">`SameSite=None` должен использоваться для разрешения использования между сайтами cookie .</span><span class="sxs-lookup"><span data-stu-id="54b59-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="54b59-110">CookieКроме того, это утверждение `SameSite=None` должно быть помечено как `Secure` .</span><span class="sxs-lookup"><span data-stu-id="54b59-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="54b59-111">Приложения, использующие, [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) могут столкнуться с проблемами с или с, `sameSite=Lax` `sameSite=Strict` cookie поскольку `<iframe>` рассматриваются как межсайтовые сценарии.</span><span class="sxs-lookup"><span data-stu-id="54b59-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="54b59-112">Значение `SameSite=None` не разрешено [стандартом 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) и приводит к тому, что некоторые реализации обрабатывают такие cookie элементы, как `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="54b59-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="54b59-113">См. раздел [Поддержка более старых браузеров](#sob) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="54b59-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="54b59-114">Этот `SameSite=Lax` параметр работает для большинства приложений cookie .</span><span class="sxs-lookup"><span data-stu-id="54b59-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="54b59-115">Некоторые формы проверки подлинности, такие как [OpenID Connect Connect](https://openid.net/connect/) (OIDC) и [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , по умолчанию перенаправления на основе POST.</span><span class="sxs-lookup"><span data-stu-id="54b59-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="54b59-116">Перенаправления на основе POST активируют защиту обозревателя SameSite, поэтому SameSite для этих компонентов отключен.</span><span class="sxs-lookup"><span data-stu-id="54b59-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="54b59-117">Большинство имен входа [OAuth](https://oauth.net/) не затрагивается из-за различий в способах передачи запросов.</span><span class="sxs-lookup"><span data-stu-id="54b59-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="54b59-118">Каждый компонент ASP.NET Core, порождаемый cookie s, должен решить, подходит ли SameSite.</span><span class="sxs-lookup"><span data-stu-id="54b59-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-identity"></a><span data-ttu-id="54b59-119">SameSite и Identity</span><span class="sxs-lookup"><span data-stu-id="54b59-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="54b59-120">Пример кода теста SameSite</span><span class="sxs-lookup"><span data-stu-id="54b59-120">SameSite test sample code</span></span>

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="54b59-121">Следующие примеры можно скачать и протестировать:</span><span class="sxs-lookup"><span data-stu-id="54b59-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="54b59-122">Образец</span><span class="sxs-lookup"><span data-stu-id="54b59-122">Sample</span></span>               | <span data-ttu-id="54b59-123">Документ</span><span class="sxs-lookup"><span data-stu-id="54b59-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="54b59-124">Платформа .NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="54b59-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="54b59-125">[Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="54b59-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54b59-126">Следующий пример можно скачать и протестировать:</span><span class="sxs-lookup"><span data-stu-id="54b59-126">The following sample can be downloaded and tested:</span></span>

| <span data-ttu-id="54b59-127">Образец</span><span class="sxs-lookup"><span data-stu-id="54b59-127">Sample</span></span>               | <span data-ttu-id="54b59-128">Документ</span><span class="sxs-lookup"><span data-stu-id="54b59-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="54b59-129">[Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="54b59-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="54b59-130">Поддержка .NET Core для атрибута sameSite</span><span class="sxs-lookup"><span data-stu-id="54b59-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="54b59-131">.NET Core 2,2 и более поздних версий поддерживают стандарт 2019 для SameSite с момента выпуска обновлений в декабре 2019.</span><span class="sxs-lookup"><span data-stu-id="54b59-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="54b59-132">Разработчики могут программно управлять значением атрибута sameSite с помощью `HttpCookie.SameSite` Свойства.</span><span class="sxs-lookup"><span data-stu-id="54b59-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="54b59-133">Если задать `SameSite` для свойства строгое, нестрогое или пустое значение, то эти значения будут записаны в сети с помощью cookie .</span><span class="sxs-lookup"><span data-stu-id="54b59-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="54b59-134">Если задано значение `(SameSiteMode)(-1)` , то атрибут sameSite не должен включаться в сеть с cookie</span><span class="sxs-lookup"><span data-stu-id="54b59-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="54b59-135">.NET Core 3,0 и более поздних версий поддерживают обновленные значения SameSite и добавляют `SameSiteMode.Unspecified` к `SameSiteMode` перечислению дополнительное значение enum.</span><span class="sxs-lookup"><span data-stu-id="54b59-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="54b59-136">Это новое значение указывает, что sameSite не следует отправлять с помощью cookie .</span><span class="sxs-lookup"><span data-stu-id="54b59-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="54b59-137">Изменения режима работы с декабрьским обновлением</span><span class="sxs-lookup"><span data-stu-id="54b59-137">December patch behavior changes</span></span>

<span data-ttu-id="54b59-138">Изменение поведения для платформа .NET Framework и .NET Core 2,1 заключается в том, как `SameSite` свойство интерпретирует это `None` значение.</span><span class="sxs-lookup"><span data-stu-id="54b59-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="54b59-139">Перед исправлением значение "не `None` создавать атрибут вообще", после исправления "выдает атрибут со значением `None` ".</span><span class="sxs-lookup"><span data-stu-id="54b59-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="54b59-140">После того как исправление будет иметь `SameSite` значение, `(SameSiteMode)(-1)` атрибут не будет выдаваться.</span><span class="sxs-lookup"><span data-stu-id="54b59-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="54b59-141">Значение SameSite по умолчанию для проверки подлинности форм и состояния сеанса cookie s изменено с `None` на `Lax` .</span><span class="sxs-lookup"><span data-stu-id="54b59-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="54b59-142">Использование API с SameSite</span><span class="sxs-lookup"><span data-stu-id="54b59-142">API usage with SameSite</span></span>

<span data-ttu-id="54b59-143">[HttpContext. Response. Cookie s. Добавление](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) значений по умолчанию в значение `Unspecified` , означающее, что атрибут SameSite не добавлен в cookie и клиент будет использовать поведение по умолчанию (нестрогий для новых браузеров, нет для старых).</span><span class="sxs-lookup"><span data-stu-id="54b59-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="54b59-144">В следующем коде показано, как изменить cookie значение SameSite на `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="54b59-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="54b59-145">Все компоненты ASP.NET Core, которые выдают cookie s, переопределяют предыдущие значения по умолчанию параметрами, подходящими для их сценариев.</span><span class="sxs-lookup"><span data-stu-id="54b59-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="54b59-146">Переопределенные ранее значения по умолчанию не изменились.</span><span class="sxs-lookup"><span data-stu-id="54b59-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="54b59-147">Компонент</span><span class="sxs-lookup"><span data-stu-id="54b59-147">Component</span></span> | cookie | <span data-ttu-id="54b59-148">По умолчанию</span><span class="sxs-lookup"><span data-stu-id="54b59-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="54b59-149">[Сессионоптионс.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="54b59-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="54b59-150">[CookieТемпдатапровидероптионс.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="54b59-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="54b59-151">[Антифоржерйоптионс.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="54b59-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="54b59-152">[Cookie Идентификаци](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="54b59-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="54b59-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="54b59-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="54b59-154">[Твиттероптионс. штат Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="54b59-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="54b59-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Ремотеаусентикатионоптионс. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="54b59-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="54b59-156">[Опенидконнектоптионс. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="54b59-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="54b59-157">[HttpContext. Response. Cookie s. append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="54b59-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="54b59-158">ASP.NET Core 3,1 и более поздних версий предоставляет следующую поддержку SameSite:</span><span class="sxs-lookup"><span data-stu-id="54b59-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="54b59-159">Переопределяет поведение `SameSiteMode.None` для выпуска `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="54b59-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="54b59-160">Добавляет новое значение `SameSiteMode.Unspecified` , чтобы опустить атрибут SameSite.</span><span class="sxs-lookup"><span data-stu-id="54b59-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="54b59-161">Все cookie API-интерфейсы s по умолчанию имеют значение `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="54b59-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="54b59-162">Некоторые компоненты, использующие cookie s, задают значения, более специфичные для их сценариев.</span><span class="sxs-lookup"><span data-stu-id="54b59-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="54b59-163">Примеры см. в таблице выше.</span><span class="sxs-lookup"><span data-stu-id="54b59-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54b59-164">В ASP.NET Core 3,0 и более поздних версиях значения SameSite по умолчанию были изменены, чтобы избежать конфликтов с несовместимыми по умолчанию для клиентов.</span><span class="sxs-lookup"><span data-stu-id="54b59-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="54b59-165">Следующие API-интерфейсы изменили значение по умолчанию с `SameSiteMode.Lax ` на, `-1` чтобы избежать выпуска атрибута SameSite для этих cookie s:</span><span class="sxs-lookup"><span data-stu-id="54b59-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="54b59-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> используется с [HttpContext. Response. Cookie s. append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="54b59-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="54b59-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  используется в качестве фабрики для `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="54b59-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="54b59-168">[CookieПолициоптионс. Минимумсамеситеполици](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="54b59-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="54b59-169">Журнал и изменения</span><span class="sxs-lookup"><span data-stu-id="54b59-169">History and changes</span></span>

<span data-ttu-id="54b59-170">Поддержка SameSite была впервые реализована в ASP.NET Core в 2,0 с использованием [черновой стандартов 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="54b59-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="54b59-171">Стандарт 2016 был явно принят.</span><span class="sxs-lookup"><span data-stu-id="54b59-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="54b59-172">ASP.NET Core участие, задав по cookie умолчанию несколько s `Lax` .</span><span class="sxs-lookup"><span data-stu-id="54b59-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="54b59-173">После обнаружения нескольких [проблем](https://github.com/aspnet/Announcements/issues/318) с проверкой подлинности большая часть использования SameSite была [отключена](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="54b59-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="54b59-174">[Исправления](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) были выпущены в ноябре 2019, чтобы обновить стандарт 2016 до стандарта 2019.</span><span class="sxs-lookup"><span data-stu-id="54b59-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="54b59-175">[2019. черновик спецификации SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="54b59-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="54b59-176">**Не** имеет обратной совместимости с черновиком 2016.</span><span class="sxs-lookup"><span data-stu-id="54b59-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="54b59-177">Дополнительные сведения см. в разделе [поддержка старых браузеров](#sob) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="54b59-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="54b59-178">Указывает, cookie что параметр s обрабатывается как по `SameSite=Lax` умолчанию.</span><span class="sxs-lookup"><span data-stu-id="54b59-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="54b59-179">Указывает cookie , что явное утверждение `SameSite=None` для включения межсайтовой доставки должно быть помечено как `Secure` .</span><span class="sxs-lookup"><span data-stu-id="54b59-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="54b59-180">`None` — Новая запись для отказаться.</span><span class="sxs-lookup"><span data-stu-id="54b59-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="54b59-181">Поддерживается исправлениями, выпущенными для ASP.NET Core 2,1, 2,2 и 3,0.</span><span class="sxs-lookup"><span data-stu-id="54b59-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="54b59-182">ASP.NET Core 3,1 имеет дополнительную поддержку SameSite.</span><span class="sxs-lookup"><span data-stu-id="54b59-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="54b59-183">По умолчанию в [фев 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)планируется включить по [Chrome](https://chromestatus.com/feature/5088147346030592) .</span><span class="sxs-lookup"><span data-stu-id="54b59-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="54b59-184">Браузеры начали переходить к этому стандарту в 2019.</span><span class="sxs-lookup"><span data-stu-id="54b59-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="54b59-185">API-интерфейсы, затрагиваемые изменением стандарта 2016 SameSite с черновым стандартом на версию 2019</span><span class="sxs-lookup"><span data-stu-id="54b59-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="54b59-186">HTTP. Самеситемоде</span><span class="sxs-lookup"><span data-stu-id="54b59-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="54b59-187">[CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="54b59-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="54b59-188">[CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="54b59-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="54b59-189">[CookieПолициоптионс. Минимумсамеситеполици](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="54b59-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="54b59-190">Поддержка старых браузеров</span><span class="sxs-lookup"><span data-stu-id="54b59-190">Supporting older browsers</span></span>

<span data-ttu-id="54b59-191">Стандарт 2016 SameSite предписание, что неизвестные значения должны рассматриваться как `SameSite=Strict` значения.</span><span class="sxs-lookup"><span data-stu-id="54b59-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="54b59-192">Приложения, доступ к которым осуществляется из старых браузеров, поддерживающих стандарт 2016 SameSite, могут прерываться при получении свойства SameSite со значением `None` .</span><span class="sxs-lookup"><span data-stu-id="54b59-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="54b59-193">Веб-приложения должны реализовывать обнаружение браузера, если они будут поддерживать старые браузеры.</span><span class="sxs-lookup"><span data-stu-id="54b59-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="54b59-194">ASP.NET Core не реализует обнаружение браузеров, так как значения User-Agents являются очень энергонезависимы и часто меняются.</span><span class="sxs-lookup"><span data-stu-id="54b59-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="54b59-195">Точка расширения в <xref:Microsoft.AspNetCore.CookiePolicy> позволяет подключать User-Agent конкретной логики.</span><span class="sxs-lookup"><span data-stu-id="54b59-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="54b59-196">В `Startup.Configure` добавьте код, вызывающий <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> перед вызовом <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> метода, или *любой* метод, который выполняет запись cookie s:</span><span class="sxs-lookup"><span data-stu-id="54b59-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="54b59-197">В `Startup.ConfigureServices` добавьте код, аналогичный приведенному ниже:</span><span class="sxs-lookup"><span data-stu-id="54b59-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="54b59-198">В предыдущем примере `MyUserAgentDetectionLib.DisallowsSameSiteNone` — это библиотека, предоставляемая пользователем и определяющая, не поддерживает ли агент пользователя SameSite `None` :</span><span class="sxs-lookup"><span data-stu-id="54b59-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="54b59-199">В следующем коде показан пример `DisallowsSameSiteNone` метода:</span><span class="sxs-lookup"><span data-stu-id="54b59-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="54b59-200">Следующий код предназначен только для демонстрации:</span><span class="sxs-lookup"><span data-stu-id="54b59-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="54b59-201">Его не следует считать полным.</span><span class="sxs-lookup"><span data-stu-id="54b59-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="54b59-202">Он не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="54b59-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="54b59-203">Тестирование приложений на наличие проблем SameSite</span><span class="sxs-lookup"><span data-stu-id="54b59-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="54b59-204">Приложения, взаимодействующие с удаленными сайтами, например с помощью имени входа от стороннего разработчика, должны:</span><span class="sxs-lookup"><span data-stu-id="54b59-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="54b59-205">Протестируйте взаимодействие в нескольких браузерах.</span><span class="sxs-lookup"><span data-stu-id="54b59-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="54b59-206">Примените [ Cookie Обнаружение и устранение проблем обозревателя политик,](#sob) описанные в этом документе.</span><span class="sxs-lookup"><span data-stu-id="54b59-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="54b59-207">Протестируйте веб-приложения с помощью версии клиента, которая может явно использовать новое поведение SameSite.</span><span class="sxs-lookup"><span data-stu-id="54b59-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="54b59-208">У Chrome, Firefox и Chromium ребра есть новые флаги функций выбора, которые можно использовать для тестирования.</span><span class="sxs-lookup"><span data-stu-id="54b59-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="54b59-209">Когда приложение применяет исправления SameSite, протестируйте его с помощью более старых версий клиента, особенно Safari.</span><span class="sxs-lookup"><span data-stu-id="54b59-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="54b59-210">Дополнительные сведения см. в разделе [поддержка старых браузеров](#sob) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="54b59-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="54b59-211">Тестирование с помощью Chrome</span><span class="sxs-lookup"><span data-stu-id="54b59-211">Test with Chrome</span></span>

<span data-ttu-id="54b59-212">Chrome 78 + дает неверные результаты, так как это временное устранение рисков.</span><span class="sxs-lookup"><span data-stu-id="54b59-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="54b59-213">Временная защита "Chrome 78 +" допускает более cookie двух минут со старыми.</span><span class="sxs-lookup"><span data-stu-id="54b59-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="54b59-214">Chrome 76 или 77 с включенными соответствующими флагами тестирования предоставляют более точные результаты.</span><span class="sxs-lookup"><span data-stu-id="54b59-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="54b59-215">Чтобы протестировать новое поведение SameSite `chrome://flags/#same-site-by-default-cookies` , переключите на **Enabled**.</span><span class="sxs-lookup"><span data-stu-id="54b59-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="54b59-216">Более старые версии Chrome (75 и ниже) передаются в отчет о сбое с новым `None` параметром.</span><span class="sxs-lookup"><span data-stu-id="54b59-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="54b59-217">См. раздел [Поддержка более старых браузеров](#sob) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="54b59-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="54b59-218">Google не делает доступными более старые версии Chrome.</span><span class="sxs-lookup"><span data-stu-id="54b59-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="54b59-219">Следуйте инструкциям в [скачивании Chromium](https://www.chromium.org/getting-involved/download-chromium) , чтобы протестировать старые версии Chrome.</span><span class="sxs-lookup"><span data-stu-id="54b59-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="54b59-220">**Не** скачивать Chrome из ссылок, предоставленных при поиске старых версий Chrome.</span><span class="sxs-lookup"><span data-stu-id="54b59-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="54b59-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="54b59-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="54b59-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="54b59-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="54b59-223">Начиная с версии ранний `80.0.3975.0` , в целях тестирования можно отключить слабый + разрешающую временную ОТправку, используя новый флаг, `--enable-features=SameSiteDefaultChecksMethodRigorously` чтобы разрешить тестирование сайтов и служб в конечном итоге в состоянии, когда устранение рисков было удалено.</span><span class="sxs-lookup"><span data-stu-id="54b59-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="54b59-224">Дополнительные сведения см. в статье Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) .</span><span class="sxs-lookup"><span data-stu-id="54b59-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="54b59-225">Тестирование с помощью Safari</span><span class="sxs-lookup"><span data-stu-id="54b59-225">Test with Safari</span></span>

<span data-ttu-id="54b59-226">Safari 12 строго реализовал ранее созданный черновик и завершается ошибкой, если новое `None` значение находится в cookie .</span><span class="sxs-lookup"><span data-stu-id="54b59-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="54b59-227">`None` не рекомендуется с помощью кода обнаружения браузеров, [поддерживающего более старые браузеры](#sob) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="54b59-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="54b59-228">Протестируйте имена входа в стиле ОС Safari 12, Safari 13 и WebKit с помощью MSAL, ADAL или любой используемой библиотеки.</span><span class="sxs-lookup"><span data-stu-id="54b59-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="54b59-229">Эта проблема зависит от базовой версии ОС.</span><span class="sxs-lookup"><span data-stu-id="54b59-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="54b59-230">Известно, что в OSX Можаве (10,14) и iOS 12 возникают проблемы совместимости с новым поведением SameSite.</span><span class="sxs-lookup"><span data-stu-id="54b59-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="54b59-231">Обновление операционной системы до OSX Catalina (10,15) или iOS 13 решает проблему.</span><span class="sxs-lookup"><span data-stu-id="54b59-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="54b59-232">В настоящее время Safari не имеет флага согласия для проверки нового поведения спецификации.</span><span class="sxs-lookup"><span data-stu-id="54b59-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="54b59-233">Тестирование с помощью Firefox</span><span class="sxs-lookup"><span data-stu-id="54b59-233">Test with Firefox</span></span>

<span data-ttu-id="54b59-234">Поддержку Firefox для нового стандарта можно протестировать на странице версии 68 +, заменив на ней `about:config` флагом функции `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="54b59-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="54b59-235">В предыдущих версиях Firefox не было отчетов о проблемах совместимости.</span><span class="sxs-lookup"><span data-stu-id="54b59-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="54b59-236">Тестирование с помощью браузера пограничных устройств</span><span class="sxs-lookup"><span data-stu-id="54b59-236">Test with Edge browser</span></span>

<span data-ttu-id="54b59-237">Ребро поддерживает старый стандарт SameSite.</span><span class="sxs-lookup"><span data-stu-id="54b59-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="54b59-238">Версия 44 не имеет известных проблем совместимости с новым стандартом.</span><span class="sxs-lookup"><span data-stu-id="54b59-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="54b59-239">Тестирование с помощью ребра (Chromium)</span><span class="sxs-lookup"><span data-stu-id="54b59-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="54b59-240">На странице установлены флаги SameSite `edge://flags/#same-site-by-default-cookies` .</span><span class="sxs-lookup"><span data-stu-id="54b59-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="54b59-241">С пограничным Chromium проблем совместимости не обнаружено.</span><span class="sxs-lookup"><span data-stu-id="54b59-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="54b59-242">Тест с Electron</span><span class="sxs-lookup"><span data-stu-id="54b59-242">Test with Electron</span></span>

<span data-ttu-id="54b59-243">Версии Electron включают более старые версии Chromium.</span><span class="sxs-lookup"><span data-stu-id="54b59-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="54b59-244">Например, версия, Electron используемая группами, — это Chromium 66, что приводит к более старому поведению.</span><span class="sxs-lookup"><span data-stu-id="54b59-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="54b59-245">Необходимо выполнить собственное тестирование совместимости с используемой версией Electron продукта.</span><span class="sxs-lookup"><span data-stu-id="54b59-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="54b59-246">См. раздел [Поддержка более старых браузеров](#sob) в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="54b59-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b59-247">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="54b59-247">Additional resources</span></span>

* [<span data-ttu-id="54b59-248">Блог по Chromium: разработчики: готовы к созданию нового SameSite = None; Cookie Параметры безопасности</span><span class="sxs-lookup"><span data-stu-id="54b59-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="54b59-249">[SameSite cookie с описанием](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="54b59-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="54b59-250">Исправления за ноябрь 2019</span><span class="sxs-lookup"><span data-stu-id="54b59-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="54b59-251">Образец</span><span class="sxs-lookup"><span data-stu-id="54b59-251">Sample</span></span>               | <span data-ttu-id="54b59-252">Документ</span><span class="sxs-lookup"><span data-stu-id="54b59-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="54b59-253">Платформа .NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="54b59-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="54b59-254">[Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="54b59-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="54b59-255">Образец</span><span class="sxs-lookup"><span data-stu-id="54b59-255">Sample</span></span>               | <span data-ttu-id="54b59-256">Документ</span><span class="sxs-lookup"><span data-stu-id="54b59-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="54b59-257">[Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="54b59-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
