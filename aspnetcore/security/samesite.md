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
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>Работа с SameSite cookie s в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

SameSite — это стандартный черновик [IETF](https://ietf.org/about/) , предназначенный для обеспечения защиты от атак с подделкой межсайтовых запросов (CSRF). Черновик, первоначально измененный в [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), был обновлен в [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00). Обновленный стандарт не обладает обратной совместимостью с предыдущим стандартом, и ниже приведены наиболее заметные отличия.

* Cookieпо умолчанию обрабатываются без заголовка SameSite `SameSite=Lax` .
* `SameSite=None` должен использоваться для разрешения использования между сайтами cookie .
* CookieКроме того, это утверждение `SameSite=None` должно быть помечено как `Secure` .
* Приложения, использующие, [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) могут столкнуться с проблемами с или с, `sameSite=Lax` `sameSite=Strict` cookie поскольку `<iframe>` рассматриваются как межсайтовые сценарии.
* Значение `SameSite=None` не разрешено [стандартом 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) и приводит к тому, что некоторые реализации обрабатывают такие cookie элементы, как `SameSite=Strict` . См. раздел [Поддержка более старых браузеров](#sob) в этом документе.

Этот `SameSite=Lax` параметр работает для большинства приложений cookie . Некоторые формы проверки подлинности, такие как [OpenID Connect Connect](https://openid.net/connect/) (OIDC) и [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , по умолчанию перенаправления на основе POST. Перенаправления на основе POST активируют защиту обозревателя SameSite, поэтому SameSite для этих компонентов отключен. Большинство имен входа [OAuth](https://oauth.net/) не затрагивается из-за различий в способах передачи запросов.

Каждый компонент ASP.NET Core, порождаемый cookie s, должен решить, подходит ли SameSite.

## <a name="samesite-and-identity"></a>SameSite и Identity

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a>Пример кода теста SameSite

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Следующие примеры можно скачать и протестировать:

| Образец               | Документ |
| ----------------- | ------------ |
| [Платформа .NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Следующий пример можно скачать и протестировать:

| Образец               | Документ |
| ----------------- | ------------ |
| [Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>Поддержка .NET Core для атрибута sameSite

.NET Core 2,2 и более поздних версий поддерживают стандарт 2019 для SameSite с момента выпуска обновлений в декабре 2019. Разработчики могут программно управлять значением атрибута sameSite с помощью `HttpCookie.SameSite` Свойства. Если задать `SameSite` для свойства строгое, нестрогое или пустое значение, то эти значения будут записаны в сети с помощью cookie . Если задано значение `(SameSiteMode)(-1)` , то атрибут sameSite не должен включаться в сеть с cookie

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

.NET Core 3,0 и более поздних версий поддерживают обновленные значения SameSite и добавляют `SameSiteMode.Unspecified` к `SameSiteMode` перечислению дополнительное значение enum.
Это новое значение указывает, что sameSite не следует отправлять с помощью cookie .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>Изменения режима работы с декабрьским обновлением

Изменение поведения для платформа .NET Framework и .NET Core 2,1 заключается в том, как `SameSite` свойство интерпретирует это `None` значение. Перед исправлением значение "не `None` создавать атрибут вообще", после исправления "выдает атрибут со значением `None` ". После того как исправление будет иметь `SameSite` значение, `(SameSiteMode)(-1)` атрибут не будет выдаваться.

Значение SameSite по умолчанию для проверки подлинности форм и состояния сеанса cookie s изменено с `None` на `Lax` .

::: moniker-end

## <a name="api-usage-with-samesite"></a>Использование API с SameSite

[HttpContext. Response. Cookie s. Добавление](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) значений по умолчанию в значение `Unspecified` , означающее, что атрибут SameSite не добавлен в cookie и клиент будет использовать поведение по умолчанию (нестрогий для новых браузеров, нет для старых). В следующем коде показано, как изменить cookie значение SameSite на `SameSiteMode.Lax` :

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Все компоненты ASP.NET Core, которые выдают cookie s, переопределяют предыдущие значения по умолчанию параметрами, подходящими для их сценариев. Переопределенные ранее значения по умолчанию не изменились.

| Компонент | cookie | По умолчанию |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [Сессионоптионс.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieТемпдатапровидероптионс.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [Антифоржерйоптионс.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Cookie Идентификаци](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [Твиттероптионс. штат Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Ремотеаусентикатионоптионс. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [Опенидконнектоптионс. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. Cookie s. append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3,1 и более поздних версий предоставляет следующую поддержку SameSite:

* Переопределяет поведение `SameSiteMode.None` для выпуска `SameSite=None`
* Добавляет новое значение `SameSiteMode.Unspecified` , чтобы опустить атрибут SameSite.
* Все cookie API-интерфейсы s по умолчанию имеют значение `Unspecified` . Некоторые компоненты, использующие cookie s, задают значения, более специфичные для их сценариев. Примеры см. в таблице выше.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

В ASP.NET Core 3,0 и более поздних версиях значения SameSite по умолчанию были изменены, чтобы избежать конфликтов с несовместимыми по умолчанию для клиентов. Следующие API-интерфейсы изменили значение по умолчанию с `SameSiteMode.Lax ` на, `-1` чтобы избежать выпуска атрибута SameSite для этих cookie s:

* <xref:Microsoft.AspNetCore.Http.CookieOptions> используется с [HttpContext. Response. Cookie s. append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder>  используется в качестве фабрики для `CookieOptions`
* [CookieПолициоптионс. Минимумсамеситеполици](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Журнал и изменения

Поддержка SameSite была впервые реализована в ASP.NET Core в 2,0 с использованием [черновой стандартов 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1). Стандарт 2016 был явно принят. ASP.NET Core участие, задав по cookie умолчанию несколько s `Lax` . После обнаружения нескольких [проблем](https://github.com/aspnet/Announcements/issues/318) с проверкой подлинности большая часть использования SameSite была [отключена](https://github.com/aspnet/Announcements/issues/348).

[Исправления](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) были выпущены в ноябре 2019, чтобы обновить стандарт 2016 до стандарта 2019. [2019. черновик спецификации SameSite](https://github.com/aspnet/Announcements/issues/390):

* **Не** имеет обратной совместимости с черновиком 2016. Дополнительные сведения см. в разделе [поддержка старых браузеров](#sob) в этом документе.
* Указывает, cookie что параметр s обрабатывается как по `SameSite=Lax` умолчанию.
* Указывает cookie , что явное утверждение `SameSite=None` для включения межсайтовой доставки должно быть помечено как `Secure` . `None` — Новая запись для отказаться.
* Поддерживается исправлениями, выпущенными для ASP.NET Core 2,1, 2,2 и 3,0. ASP.NET Core 3,1 имеет дополнительную поддержку SameSite.
* По умолчанию в [фев 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)планируется включить по [Chrome](https://chromestatus.com/feature/5088147346030592) . Браузеры начали переходить к этому стандарту в 2019.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>API-интерфейсы, затрагиваемые изменением стандарта 2016 SameSite с черновым стандартом на версию 2019

* [HTTP. Самеситемоде](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookieПолициоптионс. Минимумсамеситеполици](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Поддержка старых браузеров

Стандарт 2016 SameSite предписание, что неизвестные значения должны рассматриваться как `SameSite=Strict` значения. Приложения, доступ к которым осуществляется из старых браузеров, поддерживающих стандарт 2016 SameSite, могут прерываться при получении свойства SameSite со значением `None` . Веб-приложения должны реализовывать обнаружение браузера, если они будут поддерживать старые браузеры. ASP.NET Core не реализует обнаружение браузеров, так как значения User-Agents являются очень энергонезависимы и часто меняются. Точка расширения в <xref:Microsoft.AspNetCore.CookiePolicy> позволяет подключать User-Agent конкретной логики.

В `Startup.Configure` добавьте код, вызывающий <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> перед вызовом <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> метода, или *любой* метод, который выполняет запись cookie s:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

В `Startup.ConfigureServices` добавьте код, аналогичный приведенному ниже:

::: moniker range=">= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

В предыдущем примере `MyUserAgentDetectionLib.DisallowsSameSiteNone` — это библиотека, предоставляемая пользователем и определяющая, не поддерживает ли агент пользователя SameSite `None` :

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

В следующем коде показан пример `DisallowsSameSiteNone` метода:

> [!WARNING]
> Следующий код предназначен только для демонстрации:
> * Его не следует считать полным.
> * Он не поддерживается.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Тестирование приложений на наличие проблем SameSite

Приложения, взаимодействующие с удаленными сайтами, например с помощью имени входа от стороннего разработчика, должны:

* Протестируйте взаимодействие в нескольких браузерах.
* Примените [ Cookie Обнаружение и устранение проблем обозревателя политик,](#sob) описанные в этом документе.

Протестируйте веб-приложения с помощью версии клиента, которая может явно использовать новое поведение SameSite. У Chrome, Firefox и Chromium ребра есть новые флаги функций выбора, которые можно использовать для тестирования. Когда приложение применяет исправления SameSite, протестируйте его с помощью более старых версий клиента, особенно Safari. Дополнительные сведения см. в разделе [поддержка старых браузеров](#sob) в этом документе.

### <a name="test-with-chrome"></a>Тестирование с помощью Chrome

Chrome 78 + дает неверные результаты, так как это временное устранение рисков. Временная защита "Chrome 78 +" допускает более cookie двух минут со старыми. Chrome 76 или 77 с включенными соответствующими флагами тестирования предоставляют более точные результаты. Чтобы протестировать новое поведение SameSite `chrome://flags/#same-site-by-default-cookies` , переключите на **Enabled**. Более старые версии Chrome (75 и ниже) передаются в отчет о сбое с новым `None` параметром. См. раздел [Поддержка более старых браузеров](#sob) в этом документе.

Google не делает доступными более старые версии Chrome. Следуйте инструкциям в [скачивании Chromium](https://www.chromium.org/getting-involved/download-chromium) , чтобы протестировать старые версии Chrome. **Не** скачивать Chrome из ссылок, предоставленных при поиске старых версий Chrome.

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

Начиная с версии ранний `80.0.3975.0` , в целях тестирования можно отключить слабый + разрешающую временную ОТправку, используя новый флаг, `--enable-features=SameSiteDefaultChecksMethodRigorously` чтобы разрешить тестирование сайтов и служб в конечном итоге в состоянии, когда устранение рисков было удалено. Дополнительные сведения см. в статье Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site) .

### <a name="test-with-safari"></a>Тестирование с помощью Safari

Safari 12 строго реализовал ранее созданный черновик и завершается ошибкой, если новое `None` значение находится в cookie . `None` не рекомендуется с помощью кода обнаружения браузеров, [поддерживающего более старые браузеры](#sob) в этом документе. Протестируйте имена входа в стиле ОС Safari 12, Safari 13 и WebKit с помощью MSAL, ADAL или любой используемой библиотеки. Эта проблема зависит от базовой версии ОС. Известно, что в OSX Можаве (10,14) и iOS 12 возникают проблемы совместимости с новым поведением SameSite. Обновление операционной системы до OSX Catalina (10,15) или iOS 13 решает проблему. В настоящее время Safari не имеет флага согласия для проверки нового поведения спецификации.

### <a name="test-with-firefox"></a>Тестирование с помощью Firefox

Поддержку Firefox для нового стандарта можно протестировать на странице версии 68 +, заменив на ней `about:config` флагом функции `network.cookie.sameSite.laxByDefault` . В предыдущих версиях Firefox не было отчетов о проблемах совместимости.

### <a name="test-with-edge-browser"></a>Тестирование с помощью браузера пограничных устройств

Ребро поддерживает старый стандарт SameSite. Версия 44 не имеет известных проблем совместимости с новым стандартом.

### <a name="test-with-edge-chromium"></a>Тестирование с помощью ребра (Chromium)

На странице установлены флаги SameSite `edge://flags/#same-site-by-default-cookies` . С пограничным Chromium проблем совместимости не обнаружено.

### <a name="test-with-electron"></a>Тест с Electron

Версии Electron включают более старые версии Chromium. Например, версия, Electron используемая группами, — это Chromium 66, что приводит к более старому поведению. Необходимо выполнить собственное тестирование совместимости с используемой версией Electron продукта. См. раздел [Поддержка более старых браузеров](#sob) в следующем разделе.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Блог по Chromium: разработчики: готовы к созданию нового SameSite = None; Cookie Параметры безопасности](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite cookie с описанием](https://web.dev/samesite-cookies-explained/)
* [Исправления за ноябрь 2019](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| Образец               | Документ |
| ----------------- | ------------ |
| [Платформа .NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| Образец               | Документ |
| ----------------- | ------------ |
| [Страницы .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
