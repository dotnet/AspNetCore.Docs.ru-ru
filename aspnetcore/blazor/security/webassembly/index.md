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
ms.openlocfilehash: fc2ebae6e88e312aafec790229f978c3130e64de
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106691"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a>Защита ASP.NET Core Blazor WebAssembly

Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)

Защита приложений Blazor WebAssembly обеспечивается аналогично защите одностраничных приложений (SPA). Существует несколько подходов к проверке подлинности пользователей в одностраничных приложениях, но наиболее распространенным и комплексным является использование реализации на основе [протокола OAuth 2.0](https://oauth.net/), например, [OpenID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Библиотека проверки подлинности

Blazor WebAssembly поддерживает проверку подлинности и авторизацию приложений с использованием OIDC и библиотеки [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication). Библиотека предоставляет набор примитивов для простой проверки подлинности на серверных компонентах ASP.NET Core. Библиотека включает в себя ASP.NET Core Identity с поддержкой авторизации API на основе [сервера Identity](https://identityserver.io/). Библиотеку можно использовать для проверки подлинности любого стороннего поставщика Identity (IP), который поддерживает OIDC (их называют поставщиками OpenID (OP)).

Проверка подлинности в Blazor WebAssembly реализуется с помощью библиотеки `oidc-client.js`, которая используется для управления сведениями о базовом протоколе проверки подлинности.

Кроме того, доступны другие варианты проверки подлинности одностраничных приложений, например использование файлов cookie SameSite. Однако технический проект Blazor WebAssembly реализован на основе OAuth и OIDC и представляет собой наилучший вариант для проверки подлинности в приложениях Blazor WebAssembly. По соображениям безопасности и в силу функциональных причин, [проверка подлинности на основе токенов](xref:security/anti-request-forgery#token-based-authentication), на базе [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html), была выбрана вместо [проверки подлинности на основе файлов cookie](xref:security/anti-request-forgery#cookie-based-authentication).

* Использование протокола на основе токенов позволяет уменьшить контактную зону атак, так как токены отправляются не во всех запросах.
* Конечные точки сервера не нуждаются в защите от [подделки межсайтовых запросов (CSRF)](xref:security/anti-request-forgery), так как токены отправляются явным образом. Это позволяет размещать приложения Blazor WebAssembly параллельно с приложениями MVC или Razor Pages.
* Разрешения токенов более узкие, чем разрешения файлов cookie. Например, токены нельзя использовать для управления учетной записью пользователя или изменения пароля пользователя, если такие функции не реализованы явным образом.
* У токенов короткий срок жизни (по умолчанию — один час), что ограничивает распространение атаки. Токены можно отозвать в любое время.
* Автономные токены JWT гарантируют выполнение надлежащего процесса проверки подлинности на клиенте и сервере. Например, на клиенте есть средства для обнаружения и проверки допустимости получаемых токенов, а также подтверждения их выпуска в рамках данного процесса проверки подлинности. Если третья сторона пытается изменить токен в ходе процесса проверки подлинности, клиент может обнаружить измененный токен и не использовать его.
* Токены с OAuth и OIDC обеспечивают безопасность приложения вне зависимости от поведения агента пользователя.
* Протоколы на основе токенов, такие как OAuth и OIDC, позволяют выполнять проверку подлинности и авторизацию размещенных и автономных приложений с одинаковым набором характеристик безопасности.

## <a name="authentication-process-with-oidc"></a>Процесс проверки подлинности с использованием OIDC

Библиотека [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) предлагает несколько примитивов для реализации проверки подлинности и авторизации с помощью OIDC. В общих чертах проверка подлинности работает следующим образом.

* Когда анонимный пользователь нажимает кнопку входа или запрашивает страницу с примененным атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), он перенаправляется на страницу входа приложения (`/authentication/login`).
* На странице входа библиотека проверки подлинности готовится к перенаправлению на конечную точку авторизации. Конечная точка авторизации находится вне приложения Blazor WebAssembly и может размещаться в отдельном источнике. Конечная точка определяет, прошел ли пользователь проверку подлинности, и выдает в ответ один или несколько токенов. Библиотека проверки подлинности предоставляет обратный вызов входа для получения ответа проверки подлинности.
  * Если пользователь не прошел аутентификацию, он перенаправляется в базовую систему аутентификации. Обычно это ASP.NET Core Identity.
  * Если пользователь уже прошел проверку подлинности, конечная точка авторизации создает соответствующие токены и перенаправляет браузер назад в конечную точку обратного вызова входа (`/authentication/login-callback`).
* Когда приложение Blazor WebAssembly загружает конечную точку обратного вызова входа (`/authentication/login-callback`), происходит обработка ответа проверки подлинности.
  * Если процесс проверки подлинности завершается успешно, пользователь проходит проверку подлинности и при необходимости перенаправляется по запрошенному исходному защищенному URL-адресу.
  * Если по какой-либо причине проверка подлинности завершается сбоем, пользователь направляется на страницу ошибки входа (`/authentication/login-failed`), и выводится сообщение об ошибке.

## <a name="authentication-component"></a>`Authentication`

Компонент `Authentication` (`Pages/Authentication.razor`) обрабатывает операции удаленной проверки подлинности и позволяет приложению выполнять следующие действия:

* настраивать маршруты приложения для различных состояний проверки подлинности;
* задавать содержимое пользовательского интерфейса для различных состояний проверки подлинности;
* управлять состоянием проверки подлинности.

Действия по проверке подлинности, такие как регистрация или вход пользователя в систему, передаются в компонент <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> платформы Blazor, который сохраняет и контролирует состояние в операциях проверки подлинности.

Дополнительные сведения и примеры см. в разделе <xref:blazor/security/webassembly/additional-scenarios>.

## <a name="authorization"></a>Авторизация

В приложениях Blazor WebAssembly авторизацию можно обойти, так как пользователь может изменять весь код на стороне клиента. Это же справедливо для всех технологий на стороне клиента, включая платформы одностраничного приложения JavaScript или собственных приложений для любой операционной системы.

**Всегда выполняйте проверки авторизации на стороне сервера в конечных точках API, к которым обращается клиентское приложение.**

## <a name="require-authorization-for-the-entire-app"></a>Требование авторизации для всего приложения

Примените [атрибут `[Authorize]`](xref:blazor/security/index#authorize-attribute) ([документация по API](xref:System.Web.Mvc.AuthorizeAttribute)) к каждому компоненту Razor приложения, используя один из следующих подходов.

* Используйте директиву [`@attribute`](xref:mvc/views/razor#attribute) в файле `_Imports.razor`:

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* Добавьте атрибут к каждому компоненту Razor в папке `Pages`.

> [!NOTE]
> Задание политики <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> с использованием <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> **не** поддерживается.

## <a name="refresh-tokens"></a>Маркеры обновления

Маркеры обновления не могут быть защищены на стороне клиента в приложениях Blazor WebAssembly. Поэтому маркеры обновления не должны отправляться в приложение для непосредственного использования.

Маркеры обновления могут поддерживаться и использоваться серверным приложением в размещенном решении Blazor WebAssembly для доступа к интерфейсам API сторонних производителей. Для получения дополнительной информации см. <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.

## <a name="establish-claims-for-users"></a>Создание утверждений для пользователей

Приложения часто требуют утверждений для пользователей при вызове сервера в веб-API. Например, утверждения часто используются в приложении для [получения авторизации](xref:blazor/security/index#authorization). В этих сценариях приложение запрашивает маркер доступа к службе и получает с его помощью пользовательские данные для утверждений. Для примера см. следующие ресурсы:

* [Дополнительные сценарии. Настройка пользователя](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-identity-server"></a>Служба приложений Azure в Linux с сервером Identity

При развертывании в Службе приложений Azure в Linux с сервером Identity нужно указать издателя явно. Для получения дополнительной информации см. <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="windows-authentication"></a>Проверка подлинности Windows

Не рекомендуется использовать проверку подлинности Windows с Blazor Webassembly или с любой другой платформой SPA. Вместо проверки подлинности Windows рекомендуется использовать протоколы на основе маркеров, такой как OIDC со службами федерации Active Directory (ADFS).

Если проверка подлинности Windows используется с Blazor Webassembly или с любой другой платформой SPA, необходимо принять дополнительные меры для защиты приложения от маркеров подделки межсайтовых запросов (CSRF). Те же требования, которые применяются к cookie, распространяются и на проверку подлинности Windows, с тем лишь условием, что проверка подлинности Windows не предоставляет механизма для предотвращения общего доступа к контексту проверки подлинности в разных источниках. Приложения, использующие проверку подлинности Windows без дополнительной защиты от CSRF, должны быть ограничены интрасетью организации и не должны использоваться в Интернете.

Информацию смотрите здесь: <xref:security/anti-request-forgery>.

## <a name="implementation-guidance"></a>Рекомендации по реализации

Статьи в этом *обзоре* содержат сведения о проверке подлинности пользователей в приложениях Blazor WebAssembly для конкретных поставщиков.

Автономные приложения Blazor WebAssembly:

* [Общие рекомендации по использованию поставщиков OIDC и библиотеки проверки подлинности WebAssembly](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Учетные записи Майкрософт](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

Размещенные приложения Blazor WebAssembly:

* [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Сервер Identity](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

Дополнительное руководство по конфигурации см. в следующих статьях:

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Дополнительное руководство по настройке: <xref:blazor/security/webassembly/additional-scenarios>.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:host-and-deploy/proxy-load-balancer>: Включает рекомендации по следующим вопросам:
  * Использование ПО промежуточного слоя перенаправленных заголовков для сохранения сведений о схеме HTTPS на прокси-серверах и во внутренних сетях.
  * Дополнительные сценарии и варианты использования, включая ручную настройку схемы, изменение пути запроса для правильной маршрутизации запроса и перенаправление схемы запроса для обратных прокси-серверов Linux и обратных прокси-серверов, отличных от IIS.
