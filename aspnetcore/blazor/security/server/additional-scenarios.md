---
title: Сценарии обеспечения дополнительной безопасности Blazor Server для ASP.NET Core
author: guardrex
description: Узнайте, как настроить Blazor Server для сценариев обеспечения дополнительной безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: d47cfba75b640f57cc713049594d4e8acd1fcd0e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280326"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>Сценарии обеспечения дополнительной безопасности Blazor Server для ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Передача маркеров в приложение Blazor Server</h2>

Маркеры, доступные за пределами компонентов Razor в приложении Blazor Server, можно передавать в компоненты с помощью подхода, описанного в этой статье.

Проверка подлинности приложения Blazor Server выполняется так же, как и для обычного приложения Razor Pages или MVC. Подготовьте и сохраните маркеры в файле cookie проверки подлинности. Пример:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

При необходимости дополнительные области добавляются с помощью `options.Scope.Add("{SCOPE}");`, где заполнитель `{SCOPE}` — это добавляемая дополнительная область.

Определите службу поставщика маркеров **с областью**, которую можно использовать в приложении Blazor для разрешения маркеров из [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection).

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

В `Startup.ConfigureServices` добавьте службы для следующего:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

В файле `_Host.cshtml` создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

В компоненте `App` (`App.razor`) разрешите службу и инициализируйте ее с помощью данных из параметра:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Добавьте в приложение ссылку на пакет NuGet [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).

В службе, которая выполняет запрос безопасного API, внедрите поставщик токена и получите токен для запроса API.

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Настройка схемы проверки подлинности</h2>

Для приложения, использующего более одного ПО промежуточного слоя для проверки подлинности и, таким образом, имеющего несколько схем проверки подлинности, схему, которую использует Blazor, можно явно задать в конфигурации конечной точки `Startup.Configure`. В следующем примере задается схема Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Передача маркеров в приложение Blazor Server</h2>

Маркеры, доступные за пределами компонентов Razor в приложении Blazor Server, можно передавать в компоненты с помощью подхода, описанного в этой статье.

Проверка подлинности приложения Blazor Server выполняется так же, как и для обычного приложения Razor Pages или MVC. Подготовьте и сохраните маркеры в файле cookie проверки подлинности. Пример:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

При необходимости дополнительные области добавляются с помощью `options.Scope.Add("{SCOPE}");`, где заполнитель `{SCOPE}` — это добавляемая дополнительная область.

При необходимости можно указать ресурс с помощью `options.Resource = "{RESOURCE}";`, где заполнитель `{RESOURCE}` — это ресурс. Пример:

```csharp
options.Resource = "https://graph.microsoft.com";
```

Определите класс для передачи исходного состояния приложения с маркерами доступа и обновления:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Определите службу поставщика маркеров **с областью**, которую можно использовать в приложении Blazor для разрешения маркеров из [внедрения зависимостей (DI)](xref:blazor/fundamentals/dependency-injection).

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

В `Startup.ConfigureServices` добавьте службы для следующего:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

В файле `_Host.cshtml` создайте экземпляр `InitialApplicationState` и передайте его в качестве параметра в приложение:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

В компоненте `App` (`App.razor`) разрешите службу и инициализируйте ее с помощью данных из параметра:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Добавьте в приложение ссылку на пакет NuGet [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client).

В службе, которая выполняет запрос безопасного API, внедрите поставщик токена и получите токен для запроса API.

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Настройка схемы проверки подлинности</h2>

Для приложения, использующего более одного ПО промежуточного слоя для проверки подлинности и, таким образом, имеющего несколько схем проверки подлинности, схему, которую использует Blazor, можно явно задать в конфигурации конечной точки `Startup.Configure`. В следующем примере задается схема Azure Active Directory:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Использование конечных точек OpenID Connect (OIDC) версии 2.0

До версии ASP.NET Core 5.0 в библиотеке проверки подлинности и шаблонах Blazor используются конечные точки OpenID Connect (OIDC) версии 1.0. Чтобы использовать конечную точку версии 2.0 с версиями ASP.NET Core до 5.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>.

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую. Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>, либо в файле параметров приложения с помощью ключа <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>.

### <a name="code-changes"></a>Изменения кода

* Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0. Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) документации по Azure.
* Поскольку ресурсы указываются в URI области для конечных точек версии 2.0, удалите параметр <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> свойства в <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>.

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  Дополнительные сведения см. в разделе [Области, а не ресурсы](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) в документации Azure.

### <a name="app-id-uri"></a>Универсальный код ресурса идентификатора приложения

* При использовании конечных точек версии 2.0 в интерфейсах API определяется код *`App ID URI`* , который должен представлять уникальный идентификатор интерфейса API.
* Универсальный код ресурса (URI) идентификатора приложения включается во все области в качестве префикса, а конечные точки версии 2.0 выдают маркеры доступа с кодом URI идентификатора приложения в качестве аудитории.
* При использовании конечных точек версии 2.0 идентификатор клиента, настроенный в API сервера, изменяется с идентификатора приложения API (идентификатора клиента) на код URI идентификатора приложения.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

URI идентификатора приложения для использования находится в описании регистрации приложения поставщика OIDC.

::: moniker-end
