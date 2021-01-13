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
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="32c76-103">Использование API Graph в ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="32c76-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="32c76-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="32c76-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="32c76-105">[API Microsoft Graph](/graph/use-the-api) — это веб-API с поддержкой REST, позволяющий Blazor и другим приложениям .NET Framework получать доступ к ресурсам служб Microsoft Cloud.</span><span class="sxs-lookup"><span data-stu-id="32c76-105">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="32c76-106">Пакет SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="32c76-106">Graph SDK</span></span>

<span data-ttu-id="32c76-107">[Пакеты SDK для Microsoft Graph](/graph/sdks/sdks-overview) предназначены для упрощения создания высококачественных, эффективных и устойчивых приложений, обращающихся к Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="32c76-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="32c76-108">Для примеров этого раздела в файле проекта изолированного приложения или приложения *`Client`* требуются ссылки на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="32c76-108">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="32c76-109">Следующие служебные классы и конфигурации используются в каждом из следующих подразделов этой статьи:</span><span class="sxs-lookup"><span data-stu-id="32c76-109">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="32c76-110">Вызов API Graph из компонента с помощью пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="32c76-110">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="32c76-111">Настройка утверждений пользователей с помощью пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="32c76-111">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="32c76-112">После добавления областей API Microsoft Graph в область AAD на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="32c76-112">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="32c76-113">Добавьте следующий класс `GraphClientExtensions.cs` в изолированное приложение или приложение *`Client`* размещенного решения Blazor.</span><span class="sxs-lookup"><span data-stu-id="32c76-113">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="32c76-114">Укажите необходимые области для свойства <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> из <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> в методе `AuthenticateRequestAsync`.</span><span class="sxs-lookup"><span data-stu-id="32c76-114">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="32c76-115">В следующем примере область `User.Read` указана в соответствии с примерами в последующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="32c76-115">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

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

<span data-ttu-id="32c76-116">Заполнители области `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` в приведенном выше коде представляют одну или несколько разрешенных областей.</span><span class="sxs-lookup"><span data-stu-id="32c76-116">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="32c76-117">Например, установите `Scopes` для массива строк одной области для `User.Read` для использования с примерами в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="32c76-117">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="32c76-118">В `Program.Main` (`Program.cs`) добавьте клиентские службы и конфигурацию Graph с помощью метода расширения `AddGraphClient`:</span><span class="sxs-lookup"><span data-stu-id="32c76-118">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="32c76-119">Заполнители области `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` в приведенном выше коде представляют одну или несколько разрешенных областей.</span><span class="sxs-lookup"><span data-stu-id="32c76-119">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="32c76-120">Например, передайте область `User.Read` в `AddGraphClient` для использования примеров в следующих разделах этой статьи:</span><span class="sxs-lookup"><span data-stu-id="32c76-120">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="32c76-121">Вызов API Graph из компонента с помощью пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="32c76-121">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="32c76-122">В этом разделе используются [служебные классы (`GraphClientExtensions.cs`)](#graph-sdk), описанные ранее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="32c76-122">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="32c76-123">Следующий компонент `GraphExample` использует внедренный объект `GraphServiceClient`, чтобы получить данные профиля пользователя AAD и отобразить номер его мобильного телефона:</span><span class="sxs-lookup"><span data-stu-id="32c76-123">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

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

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="32c76-124">Настройка утверждений пользователей с помощью пакета SDK для Graph</span><span class="sxs-lookup"><span data-stu-id="32c76-124">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="32c76-125">В этом разделе используются [служебные классы (`GraphClientExtensions.cs`)](#graph-sdk), описанные ранее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="32c76-125">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="32c76-126">В приведенном ниже примере приложение создает утверждение номера мобильного телефона для пользователя на основе номера телефона в его профиле AAD.</span><span class="sxs-lookup"><span data-stu-id="32c76-126">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="32c76-127">Приложение должно иметь область `User.Read` API Graph, настроенную в AAD.</span><span class="sxs-lookup"><span data-stu-id="32c76-127">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="32c76-128">В следующей настраиваемой фабрике учетных записей пользователей объект <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> платформы представляет учетную запись пользователя.</span><span class="sxs-lookup"><span data-stu-id="32c76-128">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="32c76-129">Если приложению требуется настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде.</span><span class="sxs-lookup"><span data-stu-id="32c76-129">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="32c76-130">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="32c76-130">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="32c76-131">В `Program.Main` (`Program.cs`) настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей. Если приложение использует настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="32c76-131">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

## <a name="named-client-with-graph-api"></a><span data-ttu-id="32c76-132">Именованный клиент в API Graph</span><span class="sxs-lookup"><span data-stu-id="32c76-132">Named client with Graph API</span></span>

<span data-ttu-id="32c76-133">В примере этого раздела используется именованный клиент <xref:System.Net.Http.HttpClient> для API Graph для получения номера мобильного телефона пользователя с целью обработки вызова.</span><span class="sxs-lookup"><span data-stu-id="32c76-133">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="32c76-134">Для примеров этого раздела в файле проекта изолированного приложения или приложения *`Client`* требуется ссылка на пакет [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).</span><span class="sxs-lookup"><span data-stu-id="32c76-134">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="32c76-135">Создайте следующий класс и конфигурацию проекта для работы с API Graph.</span><span class="sxs-lookup"><span data-stu-id="32c76-135">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="32c76-136">Следующий класс и конфигурация используются в каждом из следующих подразделов этой статьи:</span><span class="sxs-lookup"><span data-stu-id="32c76-136">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="32c76-137">Вызов API Graph из компонента</span><span class="sxs-lookup"><span data-stu-id="32c76-137">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="32c76-138">Настройка утверждений пользователей с помощью API Graph и именованного клиента</span><span class="sxs-lookup"><span data-stu-id="32c76-138">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="32c76-139">После добавления областей API Microsoft Graph в область AAD на портале Azure предоставьте необходимые области для настроенного обработчика приложения для API Graph.</span><span class="sxs-lookup"><span data-stu-id="32c76-139">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="32c76-140">В следующем примере настраивается обработчик для области `User.Read`.</span><span class="sxs-lookup"><span data-stu-id="32c76-140">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="32c76-141">Можно добавить дополнительные области.</span><span class="sxs-lookup"><span data-stu-id="32c76-141">Additional scopes can be added.</span></span>

<span data-ttu-id="32c76-142">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="32c76-142">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="32c76-143">В `Program.Main` (`Program.cs`) настройте именованный <xref:System.Net.Http.HttpClient> для API Graph:</span><span class="sxs-lookup"><span data-stu-id="32c76-143">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="32c76-144">Вызов API Graph из компонента</span><span class="sxs-lookup"><span data-stu-id="32c76-144">Call Graph API from a component</span></span>

<span data-ttu-id="32c76-145">В этом разделе используется [обработчик сообщений авторизации Graph (`GraphAuthorizationMessageHandler.cs`) и дополнения `Program.Main` к приложению](#named-client-with-graph-api), описанные выше в этой статье в разделе, где предоставляется именованный <xref:System.Net.Http.HttpClient> для API Graph.</span><span class="sxs-lookup"><span data-stu-id="32c76-145">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="32c76-146">В компоненте Razor:</span><span class="sxs-lookup"><span data-stu-id="32c76-146">In a Razor component:</span></span>

* <span data-ttu-id="32c76-147">Создайте <xref:System.Net.Http.HttpClient> для API Graph и выдайте запрос на данные профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="32c76-147">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="32c76-148">Класс `UserInfo.cs` обозначает необходимые свойства профиля пользователя с помощью атрибута <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> и имени JSON, используемого AAD для этих свойств.</span><span class="sxs-lookup"><span data-stu-id="32c76-148">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="32c76-149">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="32c76-149">`Pages/CallUser.razor`:</span></span>

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

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="32c76-150">Настройка утверждений пользователей с помощью API Graph и именованного клиента</span><span class="sxs-lookup"><span data-stu-id="32c76-150">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="32c76-151">В этом разделе используется [обработчик сообщений авторизации Graph (`GraphAuthorizationMessageHandler.cs`) и дополнения `Program.Main` к приложению](#named-client-with-graph-api), описанные выше в этой статье в разделе, где предоставляется именованный <xref:System.Net.Http.HttpClient> для API Graph.</span><span class="sxs-lookup"><span data-stu-id="32c76-151">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="32c76-152">В приведенном ниже примере приложение создает утверждение номера мобильного телефона для пользователя на основе номера телефона в его профиле AAD.</span><span class="sxs-lookup"><span data-stu-id="32c76-152">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="32c76-153">Приложение должно иметь область `User.Read` API Graph, настроенную в AAD.</span><span class="sxs-lookup"><span data-stu-id="32c76-153">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="32c76-154">Добавьте в приложение класс `UserInfo.cs` и обозначьте требуемые свойства профиля пользователя с помощью атрибута <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> и имени JSON, используемого AAD для этих свойств:</span><span class="sxs-lookup"><span data-stu-id="32c76-154">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="32c76-155">В следующей настраиваемой фабрике учетных записей пользователей объект <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> платформы представляет учетную запись пользователя.</span><span class="sxs-lookup"><span data-stu-id="32c76-155">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="32c76-156">Если приложению требуется настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде.</span><span class="sxs-lookup"><span data-stu-id="32c76-156">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="32c76-157">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="32c76-157">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="32c76-158">В `Program.Main` (`Program.cs`) настройте проверку подлинности MSAL для использования настраиваемой фабрики учетных записей пользователей. Если приложение использует настраиваемый класс учетной записи пользователя, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, замените <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> на этот класс в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="32c76-158">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

<span data-ttu-id="32c76-159">Предыдущий пример предназначен для приложения, использующего проверку подлинности AAD с MSAL.</span><span class="sxs-lookup"><span data-stu-id="32c76-159">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="32c76-160">Аналогичные шаблоны имеются для проверки подлинности OIDC и API.</span><span class="sxs-lookup"><span data-stu-id="32c76-160">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="32c76-161">Дополнительные сведения см. в примерах в разделе [Настройка пользователя с утверждением полезной нагрузки](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).</span><span class="sxs-lookup"><span data-stu-id="32c76-161">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
