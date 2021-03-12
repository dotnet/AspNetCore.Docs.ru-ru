---
title: Предотвращение атак с подделкой межсайтовых запросов (XSRF/CSRF) в ASP.NET Core
author: steve-smith
description: Узнайте, как предотвратить атаки на веб-приложения, в которых вредоносный веб-сайт может повлиять на взаимодействие между браузером клиента и приложением.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 12/05/2019
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
uid: security/anti-request-forgery
ms.openlocfilehash: 5d6f2915dd9b27142ac7d8ac55e68c6a26e41f81
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585790"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Предотвращение атак с подделкой межсайтовых запросов (XSRF/CSRF) в ASP.NET Core

[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Фийаз Хасан](https://twitter.com/FiyazBinHasan)и [Виктор Смит](https://ardalis.com/)

Подделка межсайтовых запросов (также известная как XSRF или CSRF) — это атака на веб-приложения, с помощью которой вредоносное веб-приложение может повлиять на взаимодействие между браузером клиента и веб-приложением, которое доверяет этому браузеру. Эти атаки возможны, поскольку веб-браузеры автоматически отправляют некоторые типы токенов проверки подлинности при каждом запросе на веб-сайт. Такая форма атаки также называется *атакой одним щелчком* или *обкрытием сеанса* , поскольку атака использует преимущества ранее проверенного сеанса пользователя.

Пример атаки CSRF:

1. Пользователь входит в систему `www.good-banking-site.com` с использованием проверки подлинности с помощью форм. Сервер проверяет подлинность пользователя и выдает ответ, который включает проверку подлинности cookie . Сайт уязвим к атакам, так как он доверяет любому запросу, который он получает с действительной проверкой подлинности cookie .
1. Пользователь посещает вредоносный веб-узел `www.bad-crook-site.com` .

   Вредоносный веб-узел `www.bad-crook-site.com` содержит HTML-форму, аналогичную следующей:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Обратите внимание, что форма `action` отправляется на уязвимый сайт, а не на вредоносный сайт. Это часть CSRF, связанная с "межсайт".

1. Пользователь нажимает кнопку Submit (отправить). Браузер выполняет запрос и автоматически включает проверку подлинности cookie для запрошенного домена `www.good-banking-site.com` .
1. Запрос выполняется на `www.good-banking-site.com` сервере с контекстом проверки подлинности пользователя и может выполнять любое действие, которое прошедший проверку подлинности пользователь разрешает.

В дополнение к сценарию, в котором пользователь нажимает кнопку для отправки формы, вредоносный сайт может:

* Запуск скрипта, который автоматически отправляет форму.
* Отправка отправки формы в виде запроса AJAX.
* Скрытие формы с помощью CSS.

В этих альтернативных сценариях не требуется никаких действий или входных данных пользователя, кроме первоначального посещения вредоносного сайта.

Использование протокола HTTPS не мешает CSRF атаке. Вредоносный сайт может отправить `https://www.good-banking-site.com/` запрос так же просто, как он может отправить небезопасный запрос.

Некоторые атаки направлены на конечные точки, отвечающие на запросы GET. в этом случае для выполнения действия можно использовать тег Image. Такая форма атаки является распространенной на сайтах форумов, которые допускают образы, но блокируют JavaScript. Приложения, изменяющие состояние запросов GET, которые изменяются при изменении переменных или ресурсов, уязвимы для атак злоумышленников. **Запросы на получение, которые изменили состояние, являются небезопасными. Рекомендуется никогда не изменять состояние запроса GET.**

CSRF атаки могут использоваться для веб-приложений, которые используют cookie s для проверки подлинности по следующим причинам.

* Браузеры хранят cookie s, выданные веб-приложением.
* Хранимые cookie s включают сеансы cookie s для пользователей, прошедших проверку подлинности.
* Браузеры отправляют все объекты, cookie связанные с доменом, в веб-приложение каждый запрос, независимо от того, как запрос к приложению был создан в браузере.

Однако атаки CSRF не ограничиваются использованием эксплойтов cookie s. Например, также уязвимы обычная и краткая проверка подлинности. После того как пользователь войдет в систему с обычной или дайджест-проверкой подлинности, браузер автоматически отправляет учетные данные, пока сеанс не &dagger; завершится.

&dagger;В этом контексте *сеанс* относится к сеансу на стороне клиента, в течение которого пользователь прошел проверку подлинности. Он не связан с сеансами на стороне сервера или по [промежуточного слоя сеанса ASP.NET Core](xref:fundamentals/app-state).

Пользователи могут защищаться от CSRF уязвимостей, предпринимая меры предосторожности.

* Выйдите из веб-приложений по завершении их использования.
* Периодически очищать браузер cookie .

Однако уязвимости CSRF являются фундаментальной проблемой с веб-приложением, а не конечным пользователем.

## <a name="authentication-fundamentals"></a>Основы проверки подлинности

CookieАутентификация на основе — это популярная форма проверки подлинности. Системы проверки подлинности на основе маркеров растут по популярности, особенно для одностраничных приложений (одностраничные приложения).

### <a name="cookie-based-authentication"></a>CookieПроверка подлинности на основе

Когда пользователь проходит проверку подлинности с использованием имени пользователя и пароля, он выдает маркер, содержащий билет проверки подлинности, который можно использовать для проверки подлинности и авторизации. Маркер сохраняется в виде cookie , сопровождающем каждый запрос, создаваемый клиентом. Создание и проверка cookie выполняется по Cookie промежуточного слоя проверки подлинности. По [промежуточного слоя](xref:fundamentals/middleware/index) пользователь сериализует субъект-пользователя в зашифрованный cookie . При последующих запросах по промежуточного слоя проверяет cookie , повторно создает субъект и назначает участника свойству [пользователя](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Проверка подлинности на основе токенов

Когда пользователь проходит проверку подлинности, ему выдается маркер (а не маркер подделки). Маркер содержит сведения о пользователе в виде [утверждений](/dotnet/framework/security/claims-based-identity-model) или маркер ссылки, который указывает приложению на обслуживание пользовательского состояния в приложении. Когда пользователь пытается получить доступ к ресурсу, который требует проверки подлинности, маркер отправляется в приложение с дополнительным заголовком авторизации в виде токена носителя. Это делает приложение без отслеживания состояния. В каждом последующем запросе маркер передается в запросе на проверку на стороне сервера. Этот токен не *зашифрован*; Он *кодируется*. На сервере маркер декодирован для доступа к его данным. Чтобы отправить маркер при последующих запросах, сохраните маркер в локальном хранилище браузера. Не стоит беспокоиться об уязвимости CSRF, если маркер хранится в локальном хранилище браузера. CSRF является проблемой, когда маркер хранится в cookie . Дополнительные сведения см. в примере кода "проблемное [соглашение GitHub" cookie добавляет два](https://github.com/dotnet/AspNetCore.Docs/issues/13369).

### <a name="multiple-apps-hosted-at-one-domain"></a>Несколько приложений, размещенных в одном домене

Общие среды размещения уязвимы для перехвата сеансов, входа CSRF и других атак.

Хотя `example1.contoso.net` и `example2.contoso.net` являются разными узлами, между узлами в домене есть неявные отношения доверия `*.contoso.net` . Это неявное отношение доверия позволяет потенциально недоверенным узлам повлиять на друг друга cookie (политики того же источника, управляющие запросами AJAX, не обязательно применяются к HTTP cookie s).

Атаки, которые используют доверенные cookie имена между приложениями, размещенными в том же домене, могут быть предотвращены за счет отсутствия общего доступа к доменам. Если каждое приложение размещается в собственном домене, для эксплойта не существует неявного cookie отношения доверия.

## <a name="aspnet-core-antiforgery-configuration"></a>Настройка защиты от подделки ASP.NET Core

> [!WARNING]
> ASP.NET Core реализует подделку с помощью [ASP.NET Core защиты данных](xref:security/data-protection/introduction). Стек защиты данных должен быть настроен для работы в ферме серверов. Дополнительные сведения см. в разделе [Настройка защиты данных](xref:security/data-protection/configuration/overview) .

::: moniker range=">= aspnetcore-3.0"

По промежуточного слоя для подделки добавляется в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) при вызове одного из следующих интерфейсов API в `Startup.ConfigureServices` .

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

По промежуточного слоя для подделки добавляется в контейнер [внедрения зависимостей](xref:fundamentals/dependency-injection) при <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> вызове в `Startup.ConfigureServices`

::: moniker-end

В ASP.NET Core 2,0 или более поздней версии [формтагхелпер](xref:mvc/views/working-with-forms#the-form-tag-helper) вставляет маркеры для защиты от подделки в элементы HTML-форм. Следующая разметка в Razor файле автоматически создает маркеры для защиты от подделки:

```cshtml
<form method="post">
    ...
</form>
```

Аналогичным образом [ихтмлхелпер. бегинформ](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) по умолчанию создает маркеры подделки, если метод формы не получает значение.

Автоматическое создание маркеров защиты от подделки для HTML-элементов форм происходит, когда `<form>` тег содержит `method="post"` атрибут и выполняется одно из следующих условий.

* Атрибут action пуст ( `action=""` ).
* Атрибут Action не указан ( `<form method="post">` ).

Автоматическое создание маркеров подделки для элементов HTML-форм можно отключить:

* Явно отключите токены защиты от подделки с помощью `asp-antiforgery` атрибута:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Элемент form не участвует в вспомогательных функциях тегов с помощью [символа Helper! символ согласия](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Удалите `FormTagHelper` из представления. `FormTagHelper`Можно удалить из представления, добавив следующую директиву в Razor представление:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [ Razor Страницы](xref:razor-pages/index) автоматически защищаются от XSRF или CSRF. Дополнительные сведения см. в разделе [XSRF/CSRF and Razor pages](xref:razor-pages/index#xsrf).

Наиболее распространенным подходом к защите от атак CSRF является использование *шаблона токена синхронизатора* (STP). STP используется, когда пользователь запрашивает страницу с данными формы:

1. Сервер отправляет клиенту маркер, связанный с удостоверением текущего пользователя.
1. Клиент отправляет на сервер токен, чтобы выполнить проверку.
1. Если сервер получает маркер, который не соответствует удостоверению аутентифицированного пользователя, запрос отклоняется.

Маркер является уникальным и непредсказуемым. Маркер также можно использовать для обеспечения надлежащей последовательности последовательности запросов (например, для обеспечения последовательности запроса: страница 1 > стр. 2 > стр. 3). Все формы в ASP.NET Core шаблонах MVC и Razor страниц создают маркеры защиты от подделки. В следующей паре примеров представления создаются маркеры защиты от подделки:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Явно добавьте токен защиты от подделки к `<form>` элементу без использования вспомогательных функций тегов с поддержкой HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

В каждом из предыдущих случаев ASP.NET Core добавляет скрытое поле формы, аналогичное следующему:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core включает три [фильтра](xref:mvc/controllers/filters) для работы с маркерами подделки:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [аутовалидатеантифоржеритокен](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [игнореантифоржеритокен](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Параметры защиты от подделки

Настройка [параметров защиты от подделки](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) в `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;Задайте свойства защиты от подделки `Cookie` с помощью свойств класса [ Cookie Builder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .

| Параметр | Описание |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Определяет параметры, используемые для создания защиты от подделки cookie . |
| [формфиелднаме](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Имя скрытого поля формы, используемое системой защиты от подделки для отображения маркеров подделки в представлениях. |
| [хеадернаме](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Имя заголовка, используемого системой защиты от подделки. Если `null` значение равно, система рассматривает только данные формы. |
| [суппрессксфрамеоптионшеадер](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Указывает, следует ли подавлять создание `X-Frame-Options` заголовка. По умолчанию заголовок создается со значением «САМЕОРИГИН». По умолчанию — `false`. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Параметр | Описание |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Определяет параметры, используемые для создания защиты от подделки cookie . |
| [CookieДомен](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Домен cookie . По умолчанию — `null`. Это свойство устарело и будет удалено в следующей версии. Взамен рекомендуется использовать Cookie . Поддомен. |
| [CookieИмя](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Имя cookie. Если значение не задано, система создает уникальное имя, начинающееся [с Cookie префикса по умолчанию](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. подделка. "). Это свойство устарело и будет удалено в следующей версии. Взамен рекомендуется использовать Cookie . Безымян. |
| [CookieПуть](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Путь, заданный для cookie . Это свойство устарело и будет удалено в следующей версии. Взамен рекомендуется использовать Cookie . Путь. |
| [формфиелднаме](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Имя скрытого поля формы, используемое системой защиты от подделки для отображения маркеров подделки в представлениях. |
| [хеадернаме](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Имя заголовка, используемого системой защиты от подделки. Если `null` значение равно, система рассматривает только данные формы. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Указывает, требуется ли протокол HTTPS в системе защиты от подделки. Если значение равно `true` , то запросы, не относящиеся к HTTPS, завершаются ошибкой. По умолчанию — `false`. Это свойство устарело и будет удалено в следующей версии. Рекомендуемая альтернатива — задать Cookie . Секуреполици. |
| [суппрессксфрамеоптионшеадер](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Указывает, следует ли подавлять создание `X-Frame-Options` заголовка. По умолчанию заголовок создается со значением «САМЕОРИГИН». По умолчанию — `false`. |

::: moniker-end

Дополнительные сведения см. в разделе [ Cookie AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Настройка функций защиты от подделки с помощью Иантифоржери

[Иантифоржери](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) предоставляет API для настройки функций защиты от подделки. `IAntiforgery` может быть запрошен в `Configure` методе `Startup` класса. В следующем примере по промежуточного слоя с домашней страницы приложения используется для создания токена защиты от подделки и его отправки в ответе в виде cookie (с использованием по умолчанию углового соглашения об именовании, описанном далее в этом разделе):

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Требовать проверку защиты от подделки

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) — это фильтр действий, который можно применить к отдельному действию, контроллеру или глобально. Запросы к действиям, к которым применен этот фильтр, блокируются, если запрос не содержит допустимый токен защиты от подделки.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

`ValidateAntiForgeryToken`Атрибут требует наличия маркера для запросов к методам действий, которые он помечает, включая HTTP-запросы GET. Если `ValidateAntiForgeryToken` атрибут применяется на контроллерах приложения, его можно переопределить с помощью `IgnoreAntiforgeryToken` атрибута.

> [!NOTE]
> ASP.NET Core не поддерживает добавление маркеров подделки для автоматического получения запросов.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Автоматически проверять маркеры подделки для ненадежных методов HTTP

ASP.NET Core приложения не создают маркеры для защиты от подделки для безопасного метода HTTP (получение, HEAD, параметры и ТРАССИРОВКа). Вместо широкого применения `ValidateAntiForgeryToken` атрибута и последующего его переопределения с атрибутами `IgnoreAntiforgeryToken` можно использовать атрибут [аутовалидатеантифоржеритокен](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) . Этот атрибут работает идентично с `ValidateAntiForgeryToken` атрибутом, за исключением того, что для запросов, выполняемых с помощью следующих методов HTTP, не требуются маркеры:

* GET
* HEAD
* OPTIONS
* TRACE

Мы рекомендуем использовать `AutoValidateAntiforgeryToken` широкие возможности для сценариев, не относящихся к API. Это гарантирует, что действия POST по умолчанию защищаются. Альтернативой является игнорирование маркеров подделки по умолчанию, если только `ValidateAntiForgeryToken` не применяется к отдельным методам действий. Более вероятно, что в этом случае метод действия POST остается незащищенным по ошибке, что оставляет приложение уязвимым для атак CSRF. Все записи должны отправить токен защиты от подделки.

Интерфейсы API не имеют автоматического механизма для отправки маркера, не являющегося cookie частью. Реализация, вероятно, зависит от реализации клиентского кода. Ниже приведены некоторые примеры.

Пример уровня класса:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Глобальный пример:

::: moniker range="< aspnetcore-3.0"

Обслуживание. AddMvc (Options = Параметры>. Filters. Add (New Аутовалидатеантифоржеритокенаттрибуте ()));

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a>Переопределение атрибутов или подделки глобальных или контроллеров

Фильтр [игнореантифоржеритокен](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) используется для устранения необходимости в токене подделки для данного действия (или контроллера). При применении этот фильтр переопределяет `ValidateAntiForgeryToken` и `AutoValidateAntiforgeryToken` фильтрует фильтры, указанные на более высоком уровне (глобально или на контроллере).

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Обновить маркеры после проверки подлинности

Токены следует обновлять после проверки подлинности пользователя путем перенаправления пользователя на страницу представления или страницы Razor .

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX и одностраничные приложения

В традиционных приложениях на основе HTML маркеры защиты от подделки передаются на сервер с помощью скрытых полей формы. В современных приложениях на основе JavaScript и одностраничные приложения многие запросы выполняются программным образом. Эти запросы AJAX могут использовать другие методы (например, заголовки запросов или cookie s) для отправки маркера.

Если cookie s используется для хранения маркеров проверки подлинности и для проверки подлинности запросов API на сервере, CSRF является потенциальной проблемой. Если для хранения маркера используется локальное хранилище, уязвимость CSRF может быть устранена, так как значения из локального хранилища не отправляются на сервер автоматически при каждом запросе. Поэтому рекомендуемым подходом является использование локального хранилища для хранения токена защиты от подделки на клиенте и отправка маркера в качестве заголовка запроса.

### <a name="javascript"></a>JavaScript

Используя JavaScript с представлениями, маркер можно создать с помощью службы в представлении. Вставьте службу [Microsoft. AspNetCore. подделка. иантифоржери](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) в представление и вызовите [жетандсторетокенс](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-cshtml[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Такой подход устраняет необходимость непосредственного использования параметров с cookie сервером или считывания их из клиента.

В предыдущем примере для чтения значения скрытого поля заголовка AJAX POST используется JavaScript.

JavaScript также может получить доступ к маркерам в cookie s и использовать cookie содержимое для создания заголовка со значением маркера.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Предполагая, что сценарий отправляет маркер в заголовке с именем `X-CSRF-TOKEN` , настройте службу защиты от подделки для поиска `X-CSRF-TOKEN` заголовка:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

В следующем примере используется JavaScript для создания запроса AJAX с соответствующим заголовком:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

В AngularJS используется соглашение для адресации CSRF. Если сервер отправляет cookie с именем `XSRF-TOKEN` , то `$http` Служба AngularJS добавляет cookie значение в заголовок при отправке запроса на сервер. Этот процесс выполняется автоматически. Заголовок не обязательно задавать в клиенте явным образом. Имя заголовка — `X-XSRF-TOKEN` . Сервер должен обнаружить этот заголовок и проверить его содержимое.

Для ASP.NET Core API для работы с этим соглашением при запуске приложения:

* Настройте приложение, чтобы предоставить маркер в cookie вызываемом `XSRF-TOKEN` .
* Настройте службу защиты от подделки для поиска заголовка с именем `X-XSRF-TOKEN` .

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="windows-authentication-and-antiforgery-cookies"></a>Проверка подлинности Windows и подделка cookie s

При использовании проверки подлинности Windows конечные точки приложения должны быть защищены от атак CSRF так же, как и для cookie s.  Браузер неявно отправляет контекст проверки подлинности на сервер, поэтому конечные точки должны быть защищены от атак CSRF.

## <a name="extend-antiforgery"></a>Расширение защиты от подделки

Тип [иантифоржеряддитионалдатапровидер](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) позволяет разработчикам расширять поведение системы защиты от CSRF, округляя дополнительные данные в каждом маркере. Метод [жетаддитионалдата](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) вызывается каждый раз при создании маркера поля, а возвращаемое значение внедряется в созданный токен. Разработчик может вернуть метку времени, nonce или любое другое значение, а затем вызвать [валидатеаддитионалдата](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) для проверки этих данных при проверке маркера. Имя пользователя клиента уже внедрено в созданные токены, поэтому нет необходимости включать эти сведения. Если маркер включает дополнительные данные, но не `IAntiForgeryAdditionalDataProvider` настроен, дополнительные данные не проверяются.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) в [открытом проекте безопасности веб-приложений](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
