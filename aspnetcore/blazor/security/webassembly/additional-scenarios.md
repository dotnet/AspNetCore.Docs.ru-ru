---
title: Сценарии обеспечения дополнительной безопасности Blazor WebAssembly для ASP.NET Core
author: guardrex
description: Узнайте, как настроить Blazor WebAssembly для сценариев обеспечения дополнительной безопасности.
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: fb5b6f75959d9933e228b0288e70498ef05efc4a
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710637"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="385d5-103">Сценарии обеспечения дополнительной безопасности Blazor WebAssembly для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="385d5-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="385d5-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="385d5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="385d5-105">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="385d5-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="385d5-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> — это <xref:System.Net.Http.DelegatingHandler>, который используется для присоединения маркеров доступа к исходящим экземплярам <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="385d5-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="385d5-107">Токены получаются с помощью службы <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>, которая регистрируется платформой.</span><span class="sxs-lookup"><span data-stu-id="385d5-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="385d5-108">Если маркер получить невозможно, создается исключение <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>.</span><span class="sxs-lookup"><span data-stu-id="385d5-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="385d5-109">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> есть <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, с помощью которого можно направить пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="385d5-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="385d5-110">Для удобства платформа предоставляет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> предварительно настроенным с базовым адресом приложения как разрешенным URL-адресом.</span><span class="sxs-lookup"><span data-stu-id="385d5-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="385d5-111">**Маркеры доступа добавляются, только если URI запроса находится в базовом URI приложения.**</span><span class="sxs-lookup"><span data-stu-id="385d5-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="385d5-112">Если URI исходящих запросов не находятся в базовом URI приложения, используйте [настраиваемый класс `AuthorizationMessageHandler` (*рекомендуется*)](#custom-authorizationmessagehandler-class) или [настройте `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="385d5-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="385d5-113">Помимо настройки клиентского приложения для доступа к API сервера, серверный API должен также разрешить запросы независимо от источника (CORS), если клиент и сервер не находятся по одному и тому же базовому адресу.</span><span class="sxs-lookup"><span data-stu-id="385d5-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="385d5-114">Дополнительные сведения о конфигурации CORS на стороне сервера см. в подразделе [Общий доступ к ресурсам независимо от источника (CORS)](#cross-origin-resource-sharing-cors) далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="385d5-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="385d5-115">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="385d5-115">In the following example:</span></span>

* <span data-ttu-id="385d5-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> добавляет <xref:System.Net.Http.IHttpClientFactory> и связанные службы в коллекцию служб и настраивает именованный <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="385d5-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="385d5-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> — это базовый адрес URI ресурса при отправке запросов.</span><span class="sxs-lookup"><span data-stu-id="385d5-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="385d5-118"><xref:System.Net.Http.IHttpClientFactory> предоставляется пакетом NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).</span><span class="sxs-lookup"><span data-stu-id="385d5-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="385d5-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> — это <xref:System.Net.Http.DelegatingHandler>, который используется для присоединения маркеров доступа к исходящим экземплярам <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="385d5-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="385d5-120">Маркеры доступа добавляются, только если URI запроса находится в базовом URI приложения.</span><span class="sxs-lookup"><span data-stu-id="385d5-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="385d5-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> создает и настраивает экземпляр <xref:System.Net.Http.HttpClient> для исходящих запросов с использованием конфигурации, соответствующей именованному <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="385d5-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="385d5-122">Для приложения Blazor, основанного на шаблоне проекта, размещенном в Blazor WebAssembly, по умолчанию URI запроса находятся в базовом URI приложений.</span><span class="sxs-lookup"><span data-stu-id="385d5-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="385d5-123">Таким образом, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> в приложении, созданном из шаблона проекта.</span><span class="sxs-lookup"><span data-stu-id="385d5-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="385d5-124">Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):</span><span class="sxs-lookup"><span data-stu-id="385d5-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Http.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="385d5-125">Пользовательский класс `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="385d5-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="385d5-126">*Руководство в этом разделе рекомендуется для клиентских приложений, которые делают исходящие запросы к URI, которые не находятся в базовом URI приложения.*</span><span class="sxs-lookup"><span data-stu-id="385d5-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="385d5-127">В следующем примере пользовательский класс расширяет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для использования в качестве <xref:System.Net.Http.DelegatingHandler> для <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="385d5-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="385d5-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> настраивает этот обработчик для авторизации исходящих HTTP-запросов с помощью маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="385d5-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="385d5-129">Маркер доступа прикрепляется, только если по крайней мере один из разрешенных URL-адресов является базовым для URI запроса (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="385d5-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="385d5-130">В `Program.Main` (`Program.cs`) `CustomAuthorizationMessageHandler` регистрируется как служба с ограниченной областью действия и настраивается как <xref:System.Net.Http.DelegatingHandler> для исходящих экземпляров <xref:System.Net.Http.HttpResponseMessage>, созданных с помощью именованного <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="385d5-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="385d5-131">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="385d5-132">Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="385d5-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="385d5-133">Когда клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)), при выполнении запросов к интерфейсу API сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа.</span><span class="sxs-lookup"><span data-stu-id="385d5-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="385d5-134">Если URI запроса является относительным URI, как показано в следующем примере (`ExampleAPIMethod`), он объединяется с <xref:System.Net.Http.HttpClient.BaseAddress>, когда клиентское приложение выполняет запрос.</span><span class="sxs-lookup"><span data-stu-id="385d5-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="385d5-135">Настройка `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="385d5-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="385d5-136">Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно настроить разрешенные URL-адреса, области и URL-адреса возврата с помощью метода <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="385d5-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="385d5-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> настраивает обработчик для авторизации исходящих HTTP-запросов с помощью маркера доступа.</span><span class="sxs-lookup"><span data-stu-id="385d5-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="385d5-138">Маркер доступа прикрепляется, только если по крайней мере один из разрешенных URL-адресов является базовым для URI запроса (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span><span class="sxs-lookup"><span data-stu-id="385d5-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="385d5-139">Если URI запроса является относительным URI, он объединяется с <xref:System.Net.Http.HttpClient.BaseAddress>.</span><span class="sxs-lookup"><span data-stu-id="385d5-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="385d5-140">В следующем примере обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивает объект <xref:System.Net.Http.HttpClient> в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="385d5-141">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> назначается по умолчанию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="385d5-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="385d5-142">свойству <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`);</span><span class="sxs-lookup"><span data-stu-id="385d5-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="385d5-143">URL-адресу массива `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="385d5-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="385d5-144">Типизированный `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="385d5-144">Typed `HttpClient`</span></span>

<span data-ttu-id="385d5-145">Можно определить типизированный клиент, который будет обрабатывать все аспекты, связанные с HTTP и получением маркеров, в пределах одного класса.</span><span class="sxs-lookup"><span data-stu-id="385d5-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="385d5-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="385d5-146">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient http;
 
    public WeatherForecastClient(HttpClient http)
    {
        this.http = http;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="385d5-147">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="385d5-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="385d5-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="385d5-149">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="385d5-150">Компонент `FetchData` (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="385d5-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="385d5-151">Настройка обработчика `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="385d5-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="385d5-152">Обработчик можно дополнительно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="385d5-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="385d5-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="385d5-154">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> назначается по умолчанию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="385d5-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="385d5-155">свойству <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`);</span><span class="sxs-lookup"><span data-stu-id="385d5-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="385d5-156">URL-адресу массива `authorizedUrls`.</span><span class="sxs-lookup"><span data-stu-id="385d5-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="385d5-157">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="385d5-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="385d5-158">Если приложение Blazor WebAssembly обычно использует защищенный клиент <xref:System.Net.Http.HttpClient> по умолчанию, приложение также может выполнять неавторизованные или неаутентифицированные запросы к веб-API путем настройки именованного клиента <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="385d5-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="385d5-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="385d5-160">Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="385d5-161">Приведенная выше регистрация является дополнением к существующей регистрации защищенного клиента <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="385d5-162">Компонент создает клиент <xref:System.Net.Http.HttpClient> на основе <xref:System.Net.Http.IHttpClientFactory> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) для выполнения не прошедших проверку или неавторизованных запросов:</span><span class="sxs-lookup"><span data-stu-id="385d5-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="385d5-163">Контроллер в интерфейсе API сервера (`WeatherForecastNoAuthenticationController` в предыдущем примере) не помечен атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="385d5-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="385d5-164">Решение о том, следует ли использовать безопасный клиент или небезопасный клиент в качестве экземпляра <xref:System.Net.Http.HttpClient> по умолчанию, принимает разработчик.</span><span class="sxs-lookup"><span data-stu-id="385d5-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="385d5-165">При принятии такого решения рекомендуется учесть соотношение неавторизованных и аутентифицированных конечных точек, к которым обращается приложение.</span><span class="sxs-lookup"><span data-stu-id="385d5-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="385d5-166">Если приложение отправляет большинство запросов к безопасным конечным точкам API, используйте аутентифицированный экземпляр <xref:System.Net.Http.HttpClient> в качестве значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="385d5-167">В противном случае в качестве значения по умолчанию зарегистрируйте неавторизованный экземпляр <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="385d5-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="385d5-168">Альтернативный подход к использованию <xref:System.Net.Http.IHttpClientFactory> заключается в создании [типизированного клиента](#typed-httpclient) для неаутентифицированного доступа к анонимным конечным точкам.</span><span class="sxs-lookup"><span data-stu-id="385d5-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="385d5-169">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="385d5-169">Request additional access tokens</span></span>

<span data-ttu-id="385d5-170">Маркеры доступа можно получать вручную, вызывая `IAccessTokenProvider.RequestAccessToken`.</span><span class="sxs-lookup"><span data-stu-id="385d5-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="385d5-171">В приведенном ниже примере приложение требует дополнительной области для клиента <xref:System.Net.Http.HttpClient> по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="385d5-172">В примере использования библиотеки проверки подлинности Майкрософт (MSAL) область настраивается с помощью `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="385d5-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="385d5-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="385d5-174">Заполнители `{CUSTOM SCOPE 1}` и `{CUSTOM SCOPE 2}` в предыдущем примере — это пользовательские области.</span><span class="sxs-lookup"><span data-stu-id="385d5-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="385d5-175">Метод `IAccessTokenProvider.RequestToken` предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с указанным набором областей.</span><span class="sxs-lookup"><span data-stu-id="385d5-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="385d5-176">В компоненте Razor:</span><span class="sxs-lookup"><span data-stu-id="385d5-176">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="385d5-177">Заполнители `{CUSTOM SCOPE 1}` и `{CUSTOM SCOPE 2}` в предыдущем примере — это пользовательские области.</span><span class="sxs-lookup"><span data-stu-id="385d5-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="385d5-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> возвращает:</span><span class="sxs-lookup"><span data-stu-id="385d5-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="385d5-179">`true` с пригодным для использования `token`.</span><span class="sxs-lookup"><span data-stu-id="385d5-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="385d5-180">`false`, если маркер не получен.</span><span class="sxs-lookup"><span data-stu-id="385d5-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="385d5-181">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="385d5-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="385d5-182">При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS заголовок `Authorization` должен быть разрешен политикой CORS.</span><span class="sxs-lookup"><span data-stu-id="385d5-182">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="385d5-183">Следующая политика включает в себя настройку для следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="385d5-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="385d5-184">Источники запроса (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="385d5-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="385d5-185">Любой метод (команда).</span><span class="sxs-lookup"><span data-stu-id="385d5-185">Any method (verb).</span></span>
* <span data-ttu-id="385d5-186">Заголовки `Content-Type` и `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="385d5-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="385d5-187">Чтобы разрешить пользовательский заголовок (например, `x-custom-header`), выведите список заголовков при вызове <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="385d5-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="385d5-188">Учетные данные, установленные клиентским кодом JavaScript (для свойства `credentials` задано значение `include`).</span><span class="sxs-lookup"><span data-stu-id="385d5-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="385d5-189">Размещенное решение Blazor на основе размещенного шаблона проекта Blazor, использует тот же базовый адрес для клиентских и серверных приложений.</span><span class="sxs-lookup"><span data-stu-id="385d5-189">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="385d5-190">Для <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> клиентского приложения задан универсальный код ресурса (URI) `builder.HostEnvironment.BaseAddress` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="385d5-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="385d5-191">Конфигурация CORS **не** требуется в конфигурации по умолчанию размещенного приложения, созданного из размещенного шаблона проекта Blazor.</span><span class="sxs-lookup"><span data-stu-id="385d5-191">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="385d5-192">Дополнительным клиентским приложениям, которые не размещаются в серверном проекте и не используют базовый адрес серверного приложения **требуется** конфигурация CORS в серверном проекте.</span><span class="sxs-lookup"><span data-stu-id="385d5-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="385d5-193">Дополнительные сведения см. в разделе <xref:security/cors> и компоненте "Тестер HTTP-запросов" примера приложения (`Components/HTTPRequestTester.razor`).</span><span class="sxs-lookup"><span data-stu-id="385d5-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="385d5-194">Обработка ошибок при выполнении запросов маркеров</span><span class="sxs-lookup"><span data-stu-id="385d5-194">Handle token request errors</span></span>

<span data-ttu-id="385d5-195">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью OpenID Connect (OIDC), состояние проверки подлинности сохраняется локально в приложении SPA и в поставщике Identity (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="385d5-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="385d5-196">Маркеры, которые IP-адрес выдает для пользователя, обычно действительны недолго (как правило, около часа), поэтому клиентское приложение должно регулярно получать новые маркеры.</span><span class="sxs-lookup"><span data-stu-id="385d5-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="385d5-197">В противном случае после истечения срока действия предоставленных маркеров будет выполнен выход пользователя из системы.</span><span class="sxs-lookup"><span data-stu-id="385d5-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="385d5-198">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры без повторного прохождения проверки подлинности пользователем благодаря сохранению состояния проверки подлинности или сеанса в поставщике удостоверений.</span><span class="sxs-lookup"><span data-stu-id="385d5-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="385d5-199">В некоторых случаях клиент не может получить маркер без участия пользователя, например, если по какой-либо причине пользователь явно вышел из поставщика удостоверений.</span><span class="sxs-lookup"><span data-stu-id="385d5-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="385d5-200">Такая ситуация возникает, если пользователь переходит по адресу `https://login.microsoftonline.com` и выполняет выход из системы. В этих случаях приложению не становится известно о выходе пользователя из системы немедленно. Любой маркер, который имеется у клиента, может быть уже недействительным.</span><span class="sxs-lookup"><span data-stu-id="385d5-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="385d5-201">Кроме того, клиент не может подготовить новый маркер без участия пользователя после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="385d5-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="385d5-202">Такие сценарии нехарактерны для проверки подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="385d5-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="385d5-203">Они проистекают из особенностей одностраничных приложений.</span><span class="sxs-lookup"><span data-stu-id="385d5-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="385d5-204">Одностраничному приложению, использующему файлы cookie, также не удастся вызвать интерфейс API сервера в случае удаления файла cookie для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-204">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="385d5-205">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="385d5-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="385d5-206">Для подготовки нового маркера доступа с целью вызова API пользователю может потребоваться пройти проверку подлинности повторно.</span><span class="sxs-lookup"><span data-stu-id="385d5-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="385d5-207">Даже если у клиента есть маркер, который представляется действительным, вызов сервера может завершиться ошибкой из-за того, что маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="385d5-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="385d5-208">Когда приложение запрашивает маркер, возможны два результата:</span><span class="sxs-lookup"><span data-stu-id="385d5-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="385d5-209">Запрос выполняется успешно, и приложение получает действительный маркер.</span><span class="sxs-lookup"><span data-stu-id="385d5-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="385d5-210">Запрос завершается ошибкой, и приложение должно повторно провести проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="385d5-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="385d5-211">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="385d5-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="385d5-212">Ниже приведены возможные подходы в порядке увеличения сложности.</span><span class="sxs-lookup"><span data-stu-id="385d5-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="385d5-213">Сохраните текущее состояние страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="385d5-213">Store the current page state in session storage.</span></span> <span data-ttu-id="385d5-214">Во время [события жизненного цикла `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) проверьте, можно ли восстановить состояние, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="385d5-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="385d5-215">Добавьте параметр строки запроса и используйте его, чтобы сообщить приложению о необходимости восстановить ранее сохраненное состояние.</span><span class="sxs-lookup"><span data-stu-id="385d5-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="385d5-216">Добавьте параметр строки запроса с уникальным идентификатором для сохранения данных в хранилище сеансов без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="385d5-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="385d5-217">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="385d5-217">The following example shows how to:</span></span>

* <span data-ttu-id="385d5-218">сохранить состояние перед перенаправлением на страницу входа;</span><span class="sxs-lookup"><span data-stu-id="385d5-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="385d5-219">восстановить предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="385d5-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider
...

<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var http = new HttpClient();
        http.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            http.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await http.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="385d5-220">Сохранение состояния приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="385d5-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="385d5-221">Во время операции проверки подлинности существуют случаи, когда необходимо сохранить состояние приложения, прежде чем в браузере будет выполнено перенаправление в поставщик удостоверений.</span><span class="sxs-lookup"><span data-stu-id="385d5-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="385d5-222">Например, такая потребность может возникнуть, когда вы используете контейнер состояния и хотите восстановить состояние после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="385d5-223">Вы можете использовать пользовательский объект состояния проверки подлинности для сохранения состояния приложения или ссылки на него и восстанавливать это состояние после успешного завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="385d5-224">Такой подход демонстрируется в приведенном ниже примере.</span><span class="sxs-lookup"><span data-stu-id="385d5-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="385d5-225">В приложении создается класс контейнера состояния со свойствами для хранения значений состояния приложения.</span><span class="sxs-lookup"><span data-stu-id="385d5-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="385d5-226">В приведенном ниже примере контейнер используется для хранения значения счетчика для компонента `Counter` шаблона проекта по умолчанию (`Pages/Counter.razor`).</span><span class="sxs-lookup"><span data-stu-id="385d5-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="385d5-227">Методы сериализации и десериализации контейнера основаны на <xref:System.Text.Json>.</span><span class="sxs-lookup"><span data-stu-id="385d5-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="385d5-228">Компонент `Counter` использует контейнер состояния для хранения значения `currentCount` за пределами компонента:</span><span class="sxs-lookup"><span data-stu-id="385d5-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="385d5-229">Создайте `ApplicationAuthenticationState` на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="385d5-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="385d5-230">Укажите свойство `Id`, которое служит идентификатором для локально хранящегося состояния.</span><span class="sxs-lookup"><span data-stu-id="385d5-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="385d5-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="385d5-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="385d5-232">Компонент `Authentication` (`Pages/Authentication.razor`) сохраняет и восстанавливает состояние приложения, используя локальное хранилище сеансов, с помощью методов сериализации и десериализации `StateContainer` (`GetStateForLocalStorage` и `SetStateFromLocalStorage`):</span><span class="sxs-lookup"><span data-stu-id="385d5-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="385d5-233">В этом примере для проверки подлинности используется Azure Active Directory (AAD).</span><span class="sxs-lookup"><span data-stu-id="385d5-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="385d5-234">В `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="385d5-235">`ApplicationAuthenticationState` настраивается с типом `RemoteAuthenticationState` из библиотеки проверки подлинности Майкрософт (MSAL).</span><span class="sxs-lookup"><span data-stu-id="385d5-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="385d5-236">Контейнер состояния регистрируется в контейнере службы.</span><span class="sxs-lookup"><span data-stu-id="385d5-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="385d5-237">Настройка маршрутов приложения</span><span class="sxs-lookup"><span data-stu-id="385d5-237">Customize app routes</span></span>

<span data-ttu-id="385d5-238">Для представления различных состояний проверки подлинности библиотека [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) по умолчанию использует маршруты, приведенные в таблице ниже.</span><span class="sxs-lookup"><span data-stu-id="385d5-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="385d5-239">Маршрут</span><span class="sxs-lookup"><span data-stu-id="385d5-239">Route</span></span>                            | <span data-ttu-id="385d5-240">Цель</span><span class="sxs-lookup"><span data-stu-id="385d5-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="385d5-241">Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="385d5-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="385d5-242">Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="385d5-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="385d5-243">Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="385d5-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="385d5-244">Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="385d5-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="385d5-245">Обрабатывает результат любой операции выхода.</span><span class="sxs-lookup"><span data-stu-id="385d5-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="385d5-246">Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="385d5-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="385d5-247">Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="385d5-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="385d5-248">Активирует операцию изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="385d5-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="385d5-249">Активирует операцию регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="385d5-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="385d5-250">Маршруты, представленные в предыдущей таблице, можно настраивать с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="385d5-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="385d5-251">При настройке параметров для предоставления пользовательских маршрутов убедитесь в том, что у приложения есть маршрут для обработки каждого пути.</span><span class="sxs-lookup"><span data-stu-id="385d5-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="385d5-252">В приведенном ниже примере все пути имеют префикс `/security`.</span><span class="sxs-lookup"><span data-stu-id="385d5-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="385d5-253">Компонент `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="385d5-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="385d5-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="385d5-254">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="385d5-255">Если требуются совершенно разные пути, задайте маршруты, как описано выше, и обработайте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра действия:</span><span class="sxs-lookup"><span data-stu-id="385d5-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="385d5-256">При необходимости можно разделить пользовательский интерфейс на несколько страниц.</span><span class="sxs-lookup"><span data-stu-id="385d5-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="385d5-257">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="385d5-257">Customize the authentication user interface</span></span>

<span data-ttu-id="385d5-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> содержит набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="385d5-259">Каждое состояние можно настроить, передав пользовательский объект <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span><span class="sxs-lookup"><span data-stu-id="385d5-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="385d5-260">Чтобы настроить текст, отображаемый во время первоначального входа в систему, можно изменить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> указанным ниже образом.</span><span class="sxs-lookup"><span data-stu-id="385d5-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="385d5-261">Компонент `Authentication` (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="385d5-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="385d5-262">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> есть фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в приведенной ниже таблице.</span><span class="sxs-lookup"><span data-stu-id="385d5-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="385d5-263">Маршрут</span><span class="sxs-lookup"><span data-stu-id="385d5-263">Route</span></span>                            | <span data-ttu-id="385d5-264">Fragment</span><span class="sxs-lookup"><span data-stu-id="385d5-264">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="385d5-265">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="385d5-265">Customize the user</span></span>

<span data-ttu-id="385d5-266">Пользователей, привязанных к приложению, можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="385d5-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="385d5-267">Настройка пользователя с утверждением полезной нагрузки</span><span class="sxs-lookup"><span data-stu-id="385d5-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="385d5-268">В приведенном ниже примере пользователи приложения, прошедшие проверку подлинности, получают утверждение `amr` для каждого из методов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="385d5-269">Утверждение `amr` указывает способ проверки подлинности субъекта токена в [утверждениях полезной нагрузки](/azure/active-directory/develop/access-tokens#the-amr-claim) Microsoft Identity Platform версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="385d5-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="385d5-270">В примере используется настраиваемый класс учетной записи пользователя на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span><span class="sxs-lookup"><span data-stu-id="385d5-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="385d5-271">Создайте класс, расширяющий класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span><span class="sxs-lookup"><span data-stu-id="385d5-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="385d5-272">В приведенном ниже примере свойству `AuthenticationMethod` присваивается пользовательский массив значений свойств JSON `amr`.</span><span class="sxs-lookup"><span data-stu-id="385d5-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="385d5-273">`AuthenticationMethod` заполняется платформой автоматически при проверке подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="385d5-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="385d5-274">Создайте фабрику, расширяющую <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> для создания утверждений на основе методов проверки подлинности пользователя, хранящихся в `CustomUserAccount.AuthenticationMethod`:</span><span class="sxs-lookup"><span data-stu-id="385d5-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="385d5-275">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="385d5-276">Допустима любая из следующих регистраций:</span><span class="sxs-lookup"><span data-stu-id="385d5-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="385d5-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="385d5-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="385d5-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="385d5-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="385d5-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="385d5-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="385d5-280">Группы безопасности и роли AAD с настраиваемым классом учетной записи пользователя</span><span class="sxs-lookup"><span data-stu-id="385d5-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="385d5-281">Дополнительный пример, который работает с группами безопасности AAD, ролями администратора AAD и настраиваемым классом учетной записи пользователя, см. в разделе <xref:blazor/security/webassembly/aad-groups-roles>.</span><span class="sxs-lookup"><span data-stu-id="385d5-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="385d5-282">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="385d5-282">Support prerendering with authentication</span></span>

<span data-ttu-id="385d5-283">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="385d5-283">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="385d5-284">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="385d5-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="385d5-285">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="385d5-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="385d5-286">В классе  *приложения `Client`* `Program` (`Program.cs`) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="385d5-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add...;

        builder.Services.AddScoped( ... );

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="385d5-287">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="385d5-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="385d5-288">В методе `Startup.Configure` серверного приложения замените [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) на [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="385d5-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="385d5-289">В серверном приложении создайте папку `Pages`, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="385d5-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="385d5-290">В папке серверного приложения `Pages` создайте страницу `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="385d5-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="385d5-291">Вставьте содержимое из файла `wwwroot/index.html` приложения *`Client`* в файл `Pages/_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="385d5-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="385d5-292">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="385d5-292">Update the file's contents:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="385d5-293">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="385d5-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="385d5-294">Замените тег `<div id="app">Loading...</div>` следующим:</span><span class="sxs-lookup"><span data-stu-id="385d5-294">Replace the `<div id="app">Loading...</div>` tag with the following:</span></span>

  ```cshtml
  <div id="app">
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </div>
  ```
  
  <span data-ttu-id="385d5-295">Заполнитель `{CLIENT APP ASSEMBLY NAME}` в предыдущем примере — это имя сборки клиентского приложения (например, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="385d5-295">In the preceding example, the placeholder `{CLIENT APP ASSEMBLY NAME}` is the client app's assembly name (for example `BlazorSample.Client`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="385d5-296">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="385d5-296">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="385d5-297">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="385d5-297">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
  <span data-ttu-id="385d5-298">Заполнитель `{CLIENT APP ASSEMBLY NAME}` в предыдущем примере — это имя сборки клиентского приложения (например, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="385d5-298">In the preceding example, the placeholder `{CLIENT APP ASSEMBLY NAME}` is the client app's assembly name (for example `BlazorSample.Client`).</span></span>

::: moniker-end
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="385d5-299">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="385d5-299">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="385d5-300">При аутентификации и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов аутентификации пользователя.</span><span class="sxs-lookup"><span data-stu-id="385d5-300">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="385d5-301">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="385d5-301">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="385d5-302">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="385d5-302">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="385d5-303">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="385d5-303">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="385d5-304">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="385d5-304">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="385d5-305">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="385d5-305">In this scenario:</span></span>

* <span data-ttu-id="385d5-306">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="385d5-306">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="385d5-307">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="385d5-307">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="385d5-308">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="385d5-308">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="385d5-309">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="385d5-309">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="385d5-310">Настройте Identity с помощью стороннего поставщика входа.</span><span class="sxs-lookup"><span data-stu-id="385d5-310">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="385d5-311">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="385d5-311">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="385d5-312">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="385d5-312">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="385d5-313">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="385d5-313">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="385d5-314">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="385d5-314">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="385d5-315">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="385d5-315">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="385d5-316">Затем воспользуйтесь сторонним маркером доступа для вызова ресурсов стороннего API непосредственно из Identity в клиенте.</span><span class="sxs-lookup"><span data-stu-id="385d5-316">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="385d5-317">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="385d5-317">We don't recommend this approach.</span></span> <span data-ttu-id="385d5-318">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="385d5-318">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="385d5-319">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="385d5-319">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="385d5-320">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="385d5-320">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="385d5-321">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="385d5-321">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="385d5-322">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="385d5-322">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="385d5-323">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="385d5-323">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="385d5-324">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="385d5-324">Make an API call from the client to the server API.</span></span> <span data-ttu-id="385d5-325">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="385d5-325">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="385d5-326">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="385d5-326">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="385d5-327">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="385d5-327">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="385d5-328">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="385d5-328">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="385d5-329">Использование конечных точек OpenID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="385d5-329">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="385d5-330">В библиотеке проверки подлинности и шаблонах проекта Blazor используются конечные точки OpenID Connect (OIDC) версии 1.0.</span><span class="sxs-lookup"><span data-stu-id="385d5-330">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="385d5-331">Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> маркера носителя JWT.</span><span class="sxs-lookup"><span data-stu-id="385d5-331">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="385d5-332">В следующем примере для AAD настраивается версия 2.0 путем добавления сегмента `v2.0` к свойству <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:</span><span class="sxs-lookup"><span data-stu-id="385d5-332">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="385d5-333">Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):</span><span class="sxs-lookup"><span data-stu-id="385d5-333">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="385d5-334">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую.</span><span class="sxs-lookup"><span data-stu-id="385d5-334">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="385d5-335">Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>, либо в файле параметров приложения (`appsettings.json`) с помощью ключа `Authority`.</span><span class="sxs-lookup"><span data-stu-id="385d5-335">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="385d5-336">Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="385d5-336">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="385d5-337">Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="385d5-337">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="385d5-338">Настройка и использование gRPC в компонентах</span><span class="sxs-lookup"><span data-stu-id="385d5-338">Configure and use gRPC in components</span></span>

<span data-ttu-id="385d5-339">Чтобы настроить приложение Blazor WebAssembly для использования [платформы ASP.NET Core gRPC](xref:grpc/index), выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="385d5-339">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="385d5-340">Включите gRPC-Web на сервере.</span><span class="sxs-lookup"><span data-stu-id="385d5-340">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="385d5-341">Для получения дополнительной информации см. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="385d5-341">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="385d5-342">Зарегистрируйте службы gRPC для обработчика сообщений приложения.</span><span class="sxs-lookup"><span data-stu-id="385d5-342">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="385d5-343">В следующем примере обработчик сообщений авторизации приложения настраивается для использования [службы `GreeterClient` из учебника по gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span><span class="sxs-lookup"><span data-stu-id="385d5-343">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="385d5-344">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="385d5-344">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="385d5-345">Поместите файл `.proto` в проект `Shared` размещенного решения Blazor.</span><span class="sxs-lookup"><span data-stu-id="385d5-345">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="385d5-346">Компонент в клиентском приложении может выполнять вызовы gRPC с помощью клиента gRPC (`Pages/Grpc.razor`):</span><span class="sxs-lookup"><span data-stu-id="385d5-346">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="385d5-347">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="385d5-347">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="385d5-348">Чтобы использовать свойство `Status.DebugException`, используйте [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.30.0 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="385d5-348">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="385d5-349">Для получения дополнительной информации см. <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="385d5-349">For more information, see <xref:grpc/browser>.</span></span>

## <a name="build-a-custom-version-of-the-authenticationmsal-javascript-library"></a><span data-ttu-id="385d5-350">Создание пользовательской версии библиотеки Authentication.MSAL для JavaScript</span><span class="sxs-lookup"><span data-stu-id="385d5-350">Build a custom version of the Authentication.MSAL JavaScript library</span></span>

<span data-ttu-id="385d5-351">Если приложению требуется пользовательская версия [библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js)](https://www.npmjs.com/package/@azure/msal-browser):</span><span class="sxs-lookup"><span data-stu-id="385d5-351">If an app requires a custom version of the [Microsoft Authentication Library for JavaScript (MSAL.js)](https://www.npmjs.com/package/@azure/msal-browser), perform the following steps:</span></span>

1. <span data-ttu-id="385d5-352">Убедитесь, что в системе установлен последний пакет SDK для разработчиков .NET, или получите и установите последнюю версию пакета SDK для разработчиков, как описано [ здесь](https://github.com/dotnet/installer#installers-and-binaries).</span><span class="sxs-lookup"><span data-stu-id="385d5-352">Confirm the the system has the latest developer .NET SDK or obtain and install the latest developer SDK from [.NET Core SDK: Installers and Binaries](https://github.com/dotnet/installer#installers-and-binaries).</span></span> <span data-ttu-id="385d5-353">Для этого сценария настройка внутренних веб-каналов NuGet не требуется.</span><span class="sxs-lookup"><span data-stu-id="385d5-353">Configuration of internal NuGet feeds isn't required for this scenario.</span></span>
1. <span data-ttu-id="385d5-354">Настройте репозиторий GitHub `dotnet/aspnetcore` для разработки, как описано [здесь](https://github.com/dotnet/aspnetcore/blob/main/docs/BuildFromSource.md).</span><span class="sxs-lookup"><span data-stu-id="385d5-354">Set up the `dotnet/aspnetcore` GitHub repository for development per the docs at [Build ASP.NET Core from Source](https://github.com/dotnet/aspnetcore/blob/main/docs/BuildFromSource.md).</span></span> <span data-ttu-id="385d5-355">Создайте вилку и клонируйте или скачайте ZIP-архив [репозитория dotnet/aspnetcore в GitHub](https://github.com/dotnet/aspnetcore).</span><span class="sxs-lookup"><span data-stu-id="385d5-355">Fork and clone or download a ZIP archive of the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore).</span></span>
1. <span data-ttu-id="385d5-356">Откройте файл `src/Components/WebAssembly/Authentication.Msal/src/Interop/package.json` и задайте нужную версию `@azure/msal-browser`.</span><span class="sxs-lookup"><span data-stu-id="385d5-356">Open the the `src/Components/WebAssembly/Authentication.Msal/src/Interop/package.json` file and set the desired version of `@azure/msal-browser`.</span></span> <span data-ttu-id="385d5-357">Чтобы получить список выпущенных версий, посетите веб-сайт [npm `@azure/msal-browser`](https://www.npmjs.com/package/@azure/msal-browser) и выберите вкладку **Versions** (Версии).</span><span class="sxs-lookup"><span data-stu-id="385d5-357">For a list of released versions, visit the [`@azure/msal-browser` npm website](https://www.npmjs.com/package/@azure/msal-browser) and select the **Versions** tab.</span></span>
1. <span data-ttu-id="385d5-358">Создайте проект `Authentication.Msal` в папке `src/Components/WebAssembly/Authentication.Msal/src` с помощью команды `yarn build` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="385d5-358">Build the `Authentication.Msal` project in the `src/Components/WebAssembly/Authentication.Msal/src` folder with the `yarn build` command in a command shell.</span></span>
1. <span data-ttu-id="385d5-359">Если приложение использует [сжатые ресурсы (Brotli/Gzip)](xref:blazor/host-and-deploy/webassembly#compression), необходимо сжать файл `Interop/dist/Release/AuthenticationService.js`.</span><span class="sxs-lookup"><span data-stu-id="385d5-359">If the app uses [compressed assets (Brotli/Gzip)](xref:blazor/host-and-deploy/webassembly#compression), compress the `Interop/dist/Release/AuthenticationService.js` file.</span></span>
1. <span data-ttu-id="385d5-360">Скопируйте файл `AuthenticationService.js` и сжатые версии (`.br`/`.gz`) файла, если они созданы, из папки `Interop/dist/Release` в папку `publish/wwwroot/_content/Microsoft.Authentication.WebAssembly.Msal` приложения в опубликованных ресурсах приложения.</span><span class="sxs-lookup"><span data-stu-id="385d5-360">Copy the `AuthenticationService.js` file and compressed versions (`.br`/`.gz`) of the file, if produced, from the `Interop/dist/Release` folder into the app's `publish/wwwroot/_content/Microsoft.Authentication.WebAssembly.Msal` folder in the app's published assets.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="385d5-361">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="385d5-361">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="385d5-362">`HttpClient` и `HttpRequestMessage` с параметрами запроса API Fetch</span><span class="sxs-lookup"><span data-stu-id="385d5-362">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
