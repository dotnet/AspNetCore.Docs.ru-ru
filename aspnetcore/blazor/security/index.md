---
title: Проверка подлинности и авторизация в ASP.NET Core Blazor
author: guardrex
description: Сведения о проверке подлинности и авторизации в Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: 9a14a8e16d8e50b47c479cf4d973459fbf61cec7
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280364"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>Проверка подлинности и авторизация в ASP.NET Core Blazor

ASP.NET Core поддерживает настройку и администрирование средств безопасности в приложениях Blazor.

Сценарии безопасности для приложений Blazor Server и Blazor WebAssembly отличаются. Так как приложения Blazor Server выполняются на стороне сервера, проверки авторизации могут определить следующее:

* параметры пользовательского интерфейса, предоставленные пользователю (например, какие пункты меню доступны пользователю);
* правила доступа для приложений и компонентов.

Приложения Blazor WebAssembly выполняются на стороне клиента. В этом случае авторизация используется *только* для определения отображаемых вариантов пользовательского интерфейса. Так как пользователь может изменить или обойти проверки на стороне клиента, приложение Blazor WebAssembly не может применять правила авторизации доступа.

[Соглашения об авторизации Razor Pages](xref:security/authorization/razor-pages-authorization) не применяются к маршрутизируемым компонентам Razor. Если компонент Razor, не поддерживающий маршрутизацию, [встроен в страницу](xref:blazor/components/prerendering-and-integration), соглашения об авторизации страницы оказывают косвенное влияние на компонент Razor, а также на остальную часть содержимого страницы.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> и <xref:Microsoft.AspNetCore.Identity.UserManager%601> не поддерживаются в компонентах Razor.

## <a name="authentication"></a>Проверка подлинности

Blazor использует существующие механизмы проверки подлинности ASP.NET Core для установления личности пользователя. Конкретный механизм зависит от того, как размещается приложение Blazor (Blazor WebAssembly или Blazor Server).

### <a name="blazor-webassembly-authentication"></a>Blazor WebAssembly аутентификация

В приложениях Blazor WebAssembly проверку подлинности можно обойти, так как пользователь может изменять весь код на стороне клиента. Это же справедливо для всех технологий на стороне клиента, включая платформы одностраничного приложения JavaScript или собственных приложений для любой операционной системы.

Добавьте следующий код:

* Ссылка на пакет для [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) для файла проекта приложения.
* Пространство имен `Microsoft.AspNetCore.Components.Authorization` для файла `_Imports.razor` приложения.

Вопросы обработки проверки подлинности и использования встроенной или настраиваемой службы <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> рассматриваются в следующих разделах.

Дополнительные сведения о создании и настройке приложений см. в статье <xref:blazor/security/webassembly/index>.

### <a name="blazor-server-authentication"></a>Blazor Server аутентификация

Приложения Blazor Server работают через подключение в реальном времени, созданное с помощью SignalR. [Проверка подлинности в приложениях на основе SignalR](xref:signalr/authn-and-authz) выполняется при установлении подключения. Аутентификация может выполняться на основе cookie или других маркеров носителя.

Встроенная служба <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> для приложений Blazor Server получает данные о состоянии проверки подлинности из `HttpContext.User` в ASP.NET Core. Так состояние проверки подлинности интегрируется с существующими соответствующими механизмами проверки подлинности ASP.NET Core.

Дополнительные сведения о создании и настройке приложений см. в статье <xref:blazor/security/server/index>.

## <a name="authenticationstateprovider-service"></a>Служба AuthenticationStateProvider

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> является базовой службой, которую компоненты <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> и <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> используют для получения состояния аутентификации.

Обычно вы не используете <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> напрямую. Выберите подход с использованием [компонента `AuthorizeView`](#authorizeview-component) или [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter), как описано далее в этой статье. Основной недостаток при использовании <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> напрямую заключается в том, что компонент не получает автоматического уведомления при изменении базовых данных о состоянии аутентификации.

Служба <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> может предоставить данные <xref:System.Security.Claims.ClaimsPrincipal> о текущем пользователе, как показано в следующем примере:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Если `user.Identity.IsAuthenticated` имеет значение `true`, а пользователь является <xref:System.Security.Claims.ClaimsPrincipal>, можно перечислить утверждения и оценить членство в ролях.

Дополнительные сведения о внедрении зависимостей и службах см. в статьях <xref:blazor/fundamentals/dependency-injection> и <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Реализация пользовательского AuthenticationStateProvider

Если приложению требуется пользовательский поставщик, реализуйте <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> и переопределите `GetAuthenticationStateAsync`:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

В приложении Blazor WebAssembly служба `CustomAuthStateProvider` регистрируется в `Main` `Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

В приложении Blazor Server служба `CustomAuthStateProvider` регистрируется в `Startup.ConfigureServices`:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

С помощью `CustomAuthStateProvider` в предыдущем примере все пользователи проходят проверку подлинности с использованием имени пользователя `mrfibuli`.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Предоставление состояния аутентификации в качестве каскадного параметра

Если процедурная логика требует данных о состоянии аутентификации, например при выполнении активируемых пользователем действий, для получения таких данных определите каскадный параметр типа `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Если `user.Identity.IsAuthenticated` имеет значение `true`, можно перечислить утверждения и оценить членство в ролях.

Настройте каскадный параметр `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` с помощью компонентов <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> и <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> в компоненте `App` (`App.razor`).

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

В приложении Blazor WebAssembly добавьте службы для параметров и авторизации в `Program.Main`:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

В приложении Blazor Server уже есть службы для параметров и авторизации, поэтому ничего делать не нужно.

## <a name="authorization"></a>Авторизация

После аутентификации пользователя применяются правила *авторизации*, которые определяют доступные этому пользователю действия.

Доступ обычно предоставляется или запрещается в зависимости от следующих аспектов:

* выполнена ли аутентификация (выполнен ли вход);
* есть ли у пользователя определенная *роль*;
* есть ли у пользователя определенное *утверждение*;
* выполняются ли требования *политики*.

Каждый из этих аспектов применяется здесь так же, как в приложениях ASP.NET Core MVC или Razor Pages. Дополнительные сведения о безопасности в ASP.NET Core вы найдете в статьях [о безопасности и Identity в ASP.NET Core](xref:security/index).

## <a name="authorizeview-component"></a>Компонент AuthorizeView

Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> избирательно демонстрирует содержимое пользовательского интерфейса в зависимости от того, авторизован ли пользователь. Этот подход полезен, если вам нужно просто *отображать* пользователю данные, и не использовать удостоверение пользователя в процедурной логике.

Этот компонент представляет переменную `context` типа <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, которую можно использовать для доступа к сведениям о пользователе, выполнившем вход:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Также вы можете указать другое содержимое, которое будет отображаться, если пользователь не авторизован:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

Содержимое тегов `<Authorized>` и `<NotAuthorized>` может включать произвольные элементы, например другие интерактивные компоненты.

Обработчик событий по умолчанию для авторизованного элемента, например, метод `SecureMethod` для элемента `<button>` в предыдущем примере, может вызываться только авторизованным пользователем.

Условия авторизации, такие как роли или правила для выбора вариантов пользовательского интерфейса и доступа к ним, описаны в разделе [об авторизации](#authorization).

Если условия авторизации не указаны, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> использует политику по умолчанию со следующими правилами:

* выполнившие аутентификацию (выполнившие вход) пользователи считаются авторизованными;
* не выполнившие аутентификацию (не выполнившие вход) пользователи считаются не авторизованными.

Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> можно использовать в компоненте `NavMenu` (`Shared/NavMenu.razor`) для отображения элемента списка (`<li>...</li>`) для [компонента `NavLink`](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), но следует учитывать, что при использовании этого подхода удаляется только элемент списка из отображаемых выходных данных. Пользователь по-прежнему может переходить к компоненту.

Приложения, созданные на основе шаблона проекта Blazor, который включает проверку подлинности, используют компонент `LoginDisplay`, который зависит от компонента `AuthorizeView`. Компонент `AuthorizeView` выборочно отображает пользователям содержимое для операций, связанных с Identity. Следующий пример относится к шаблону проекта Blazor WebAssembly.

`Shared/LoginDisplay.razor`:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">Log out</button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code{
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

Следующий пример относится к шаблону проекта Blazor Server и использует конечные точки ASP.NET Core Identity в области `Identity` приложения для обработки операций, связанных с Identity.

`Shared/LoginDisplay.razor`:

```razor
<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage">Hello, @context.User.Identity.Name!</a>
        <form method="post" action="Identity/Account/LogOut">
            <button type="submit" class="nav-link btn btn-link">Log out</button>
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Log in</a>
    </NotAuthorized>
</AuthorizeView>
```

### <a name="role-based-and-policy-based-authorization"></a>Авторизация на основе ролей и политик

Компонент <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> поддерживает авторизацию на основе *ролей* или *политик*.

Для авторизации на основе ролей примените параметр <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Для получения дополнительной информации см. <xref:security/authorization/roles>.

Для авторизации на основе политик примените параметр <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Авторизация на основе утверждений считается особым случаем авторизации на основе политик. Например, вы можете определить политику, которая требует наличия определенного утверждения у пользователя. Для получения дополнительной информации см. <xref:security/authorization/policies>.

Эти API-интерфейсы можно использовать в приложениях Blazor Server или Blazor WebAssembly.

Если не указано ни <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>, ни <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> использует политику по умолчанию.

### <a name="content-displayed-during-asynchronous-authentication"></a>Содержимое, отображаемое при асинхронной аутентификации

Blazor позволяет *асинхронно* определять состояние проверки подлинности. Основной сценарий для такого подхода — приложение Blazor WebAssembly, которое направляет запрос на проверку подлинности во внешнюю конечную точку.

Пока аутентификация выполняется, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> по умолчанию не отображает содержимое. Для отображения содержимого в ходе проверки подлинности используйте тег `<Authorizing>`:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Такой подход обычно не применим к приложениям Blazor Server. Приложения Blazor Server узнают состояние проверки подлинности, как только оно устанавливается. Содержимое <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> можно указать в компоненте <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> для приложения Blazor Server, но это содержимое никогда не отображается.

## <a name="authorize-attribute"></a>Атрибут [Authorize]

Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) можно использовать в компонентах Razor:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Используйте [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) только для компонентов `@page`, полученных через маршрутизатор Blazor. Авторизация выполняется только как аспект маршрутизации и *не* для дочерних компонентов, которые отображаются на странице. Чтобы разрешить отображение конкретных частей на странице, используйте вместо этого <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.

Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) также поддерживает авторизацию на основе ролей или политик. Для авторизации на основе ролей примените параметр <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Для авторизации на основе политик примените параметр <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Если не указано ни <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>, ни <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) использует политику по умолчанию со следующими правилами:

* выполнившие аутентификацию (выполнившие вход) пользователи считаются авторизованными;
* не выполнившие аутентификацию (не выполнившие вход) пользователи считаются не авторизованными.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Настройка несанкционированного содержимого для компонента маршрутизатора

Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе с компонентом <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> позволяет приложению указать пользовательское содержимое для следующих ситуаций:

* Пользователь не удовлетворяет условию [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), которое применено к компоненту. Отображается разметка для элемента [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType). Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) описан в разделе [Атрибут `[Authorize]`](#authorize-attribute)).
* Выполняется асинхронная авторизация. Как правило, это означает, что выполняется проверка подлинности пользователя. Отображается разметка для элемента [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType).
* Содержимое не найдено. Отображается разметка для элемента [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType).

В стандартном шаблоне проекта приложения Blazor Server есть компонент `App` (`App.razor`) для настройки пользовательского содержимого.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authorization in progress</h1>
                    <p>Only visible while authorization is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Содержимое тегов `<NotFound>`, `<NotAuthorized>` и `<Authorizing>` может включать произвольные элементы, например другие интерактивные компоненты.

Если тег `<NotAuthorized>` не указан, то <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> использует следующее резервное сообщение:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Уведомление об изменении состояния аутентификации

Если приложение определяет, что базовые данные о состоянии аутентификации изменились (например, пользователь вышел из системы или другой пользователь изменил роль), [ пользовательский компонент `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) может вызвать необязательный метод <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> в базовом классе <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>. Это действие информирует потребителей данных состояния аутентификации (таких, как <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) о необходимости повторно отображения с учетом новых данных.

## <a name="procedural-logic"></a>Процедурная логика

Если приложению нужно проверять правила авторизации в составе процедурной логики, используйте каскадный параметр с типом `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, чтобы получить <xref:System.Security.Claims.ClaimsPrincipal> пользователя. `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` можно комбинировать для оценки политик с другими службами, например `IAuthorizationService`.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> В компоненте приложения Blazor WebAssembly добавьте пространства имен <xref:Microsoft.AspNetCore.Authorization> и <xref:Microsoft.AspNetCore.Components.Authorization>:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Эти пространства имен можно предоставить глобально, добавив их в файл `_Imports.razor` приложения.

## <a name="troubleshoot-errors"></a>Устранение неполадок

Распространенные ошибки

* **Для авторизации требуется каскадный параметр с типом `Task\<AuthenticationState>`. Чтобы предоставить его, попробуйте использовать `CascadingAuthenticationState`.**

* **Для `authenticationStateTask` возвращается значение `null`**

Вполне вероятно, что проект не был создан на основе шаблона приложения Blazor Server с включенной проверкой подлинности. Примените `<CascadingAuthenticationState>` в качестве оболочки определенной части дерева пользовательского интерфейса, например в компоненте `App` (`App.razor`), следующим образом.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> предоставляет каскадный параметр `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, который он, в свою очередь, получает из базовой службы внедрения зависимостей <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Создание пользовательской версии библиотеки Authentication.MSAL для JavaScript](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Awesome Blazor: проверка подлинности](https://github.com/AdrienTorris/awesome-blazor#authentication) — ссылки на примеры от сообщества
