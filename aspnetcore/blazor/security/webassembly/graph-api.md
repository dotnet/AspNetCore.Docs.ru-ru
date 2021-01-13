---
title: Использование API Graph в ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как использовать API Graph с приложениями Blazor WebAssemlby.
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 58c201d6d1172c1ff82521589f988e33d5c984ae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854500"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>Использование API Graph в ASP.NET Core Blazor WebAssembly

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

::: moniker range=">= aspnetcore-5.0"

[API Microsoft Graph](/graph/use-the-api) — это веб-API с поддержкой REST, позволяющий Blazor и другим приложениям .NET Framework получать доступ к ресурсам служб Microsoft Cloud.

## <a name="graph-sdk"></a>Пакет SDK для Graph

[Пакеты SDK для Microsoft Graph](/graph/sdks/sdks-overview) предназначены для упрощения создания высококачественных, эффективных и устойчивых приложений, обращающихся к Microsoft Graph.

Для примеров этого раздела в файле проекта изолированного приложения или приложения *`Client`* требуются ссылки на следующие пакеты:

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

Следующие служебные классы и конфигурации используются в каждом из следующих подразделов этой статьи:

* [Вызов API Graph из компонента с помощью пакета SDK для Graph](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Настройка утверждений пользователей с помощью пакета SDK для Graph](#customize-user-claims-with-the-graph-sdk)

После добавления областей API Microsoft Graph в область AAD на портале Azure:

* Добавьте следующий класс `GraphClientExtensions.cs` в изолированное приложение или приложение *`Client`* размещенного решения Blazor.
* Укажите необходимые области для свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> из <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> в методе `AuthenticateRequestAsync`. В следующем примере область `User.Read` указана в соответствии с примерами в последующих разделах этой статьи.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

Заполнители области `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` в приведенном выше коде представляют одну или несколько разрешенных областей. Например, установите `Scopes` для массива строк одной области для `User.Read` для использования с примерами в следующих разделах этой статьи:

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

В `Program.Main` (`Program.cs`) добавьте клиентские службы и конфигурацию Graph с помощью метода расширения `AddGraphClient`:

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

Заполнители области `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` в приведенном выше коде представляют одну или несколько разрешенных областей. Например, передайте область `User.Read` в `AddGraphClient` для использования примеров в следующих разделах этой статьи:

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Вызов API Graph из компонента с помощью пакета SDK для Graph

В этом разделе используются [служебные классы (`GraphClientExtensions.cs`)](#graph-sdk), описанные ранее в этой статье. Следующий компонент `GraphExample` использует внедренный объект `GraphServiceClient`, чтобы получить данные профиля пользователя AAD и отобразить номер его мобильного телефона:

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a>Настройка утверждений пользователей с помощью пакета SDK для Graph

В этом разделе используются [служебные классы (`GraphClientExtensions.cs`)](#graph-sdk), описанные ранее в этой статье.

В приведенном ниже примере приложение создает утверждение номера мобильного телефона для пользователя на основе номера телефона в его профиле AAD. Приложение должно иметь область `User.Read` API Graph, настроенную в AAD.

В следующей настраиваемой фабрике учетных записей пользователей объект <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> платформы представляет учетную запись пользователя. Если приложению требуется настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде.

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
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
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
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

В `Program.Main` (`Program.cs`) настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей. Если приложение использует настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a>Именованный клиент в API Graph

В примере этого раздела используется именованный клиент <xref:System.Net.Http.HttpClient> для API Graph для получения номера мобильного телефона пользователя с целью обработки вызова.

Для примеров этого раздела в файле проекта изолированного приложения или приложения *`Client`* требуется ссылка на пакет [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).

Создайте следующий класс и конфигурацию проекта для работы с API Graph. Следующий класс и конфигурация используются в каждом из следующих подразделов этой статьи:

* [Вызов API Graph из компонента](#call-graph-api-from-a-component)
* [Настройка утверждений пользователей с помощью API Graph и именованного клиента](#customize-user-claims-with-graph-api-and-a-named-client)

После добавления областей API Microsoft Graph в область AAD на портале Azure предоставьте необходимые области для настроенного обработчика приложения для API Graph. В следующем примере настраивается обработчик для области `User.Read`. Можно добавить дополнительные области.

`GraphAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

В `Program.Main` (`Program.cs`) настройте именованный <xref:System.Net.Http.HttpClient> для API Graph:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Вызов API Graph из компонента

В этом разделе используется [обработчик сообщений авторизации Graph (`GraphAuthorizationMessageHandler.cs`) и дополнения `Program.Main` к приложению](#named-client-with-graph-api), описанные выше в этой статье в разделе, где предоставляется именованный <xref:System.Net.Http.HttpClient> для API Graph.

В компоненте Razor:

* Создайте <xref:System.Net.Http.HttpClient> для API Graph и выдайте запрос на данные профиля пользователя.
* Класс `UserInfo.cs` обозначает необходимые свойства профиля пользователя с помощью атрибута <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> и имени JSON, используемого AAD для этих свойств.

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                // Use userInfo.MobilePhone and callInfo.Message to make a call

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Настройка утверждений пользователей с помощью API Graph и именованного клиента

В этом разделе используется [обработчик сообщений авторизации Graph (`GraphAuthorizationMessageHandler.cs`) и дополнения `Program.Main` к приложению](#named-client-with-graph-api), описанные выше в этой статье в разделе, где предоставляется именованный <xref:System.Net.Http.HttpClient> для API Graph.

В приведенном ниже примере приложение создает утверждение номера мобильного телефона для пользователя на основе номера телефона в его профиле AAD. Приложение должно иметь область `User.Read` API Graph, настроенную в AAD.

Добавьте в приложение класс `UserInfo.cs` и обозначьте требуемые свойства профиля пользователя с помощью атрибута <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> и имени JSON, используемого AAD для этих свойств:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

В следующей настраиваемой фабрике учетных записей пользователей объект <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> платформы представляет учетную запись пользователя. Если приложению требуется настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде.

`CustomAccountFactory.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

В `Program.Main` (`Program.cs`) настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей. Если приложение использует настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

Предыдущий пример предназначен для приложения, использующего проверку подлинности AAD с MSAL. Аналогичные шаблоны имеются для проверки подлинности OIDC и API. Дополнительные сведения см. в примерах в разделе [Настройка пользователя с утверждением полезной нагрузки](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).
