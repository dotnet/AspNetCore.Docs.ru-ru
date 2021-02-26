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
ms.openlocfilehash: 6c3d2f93e43cf3660a12f1c8cef1d381b587f5ea
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280536"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Сценарии обеспечения дополнительной безопасности Blazor WebAssembly для ASP.NET Core

## <a name="attach-tokens-to-outgoing-requests"></a>Присоединение маркеров к исходящим запросам

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> — это <xref:System.Net.Http.DelegatingHandler>, который используется для присоединения маркеров доступа к исходящим экземплярам <xref:System.Net.Http.HttpResponseMessage>. Токены получаются с помощью службы <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>, которая регистрируется платформой. Если маркер получить невозможно, создается исключение <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>. В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> есть <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, с помощью которого можно направить пользователя к поставщику удостоверений для получения нового маркера.

Для удобства платформа предоставляет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> предварительно настроенным с базовым адресом приложения как разрешенным URL-адресом. **Маркеры доступа добавляются, только если URI запроса находится в базовом URI приложения.** Если URI исходящих запросов не находятся в базовом URI приложения, используйте [настраиваемый класс `AuthorizationMessageHandler` (*рекомендуется*)](#custom-authorizationmessagehandler-class) или [настройте `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).

> [!NOTE]
> Помимо настройки клиентского приложения для доступа к API сервера, серверный API должен также разрешить запросы независимо от источника (CORS), если клиент и сервер не находятся по одному и тому же базовому адресу. Дополнительные сведения о конфигурации CORS на стороне сервера см. в подразделе [Общий доступ к ресурсам независимо от источника (CORS)](#cross-origin-resource-sharing-cors) далее в этой статье.

В следующем примере:

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> добавляет <xref:System.Net.Http.IHttpClientFactory> и связанные службы в коллекцию служб и настраивает именованный <xref:System.Net.Http.HttpClient> (`ServerAPI`). <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> — это базовый адрес URI ресурса при отправке запросов. <xref:System.Net.Http.IHttpClientFactory> предоставляется пакетом NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http).
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> — это <xref:System.Net.Http.DelegatingHandler>, который используется для присоединения маркеров доступа к исходящим экземплярам <xref:System.Net.Http.HttpResponseMessage>. Маркеры доступа добавляются, только если URI запроса находится в базовом URI приложения.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> создает и настраивает экземпляр <xref:System.Net.Http.HttpClient> для исходящих запросов с использованием конфигурации, соответствующей именованному <xref:System.Net.Http.HttpClient> (`ServerAPI`).

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

Для приложения Blazor, основанного на шаблоне проекта, размещенном в Blazor WebAssembly, по умолчанию URI запроса находятся в базовом URI приложений. Таким образом, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> в приложении, созданном из шаблона проекта.

Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):

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

### <a name="custom-authorizationmessagehandler-class"></a>Пользовательский класс `AuthorizationMessageHandler`

*Руководство в этом разделе рекомендуется для клиентских приложений, которые делают исходящие запросы к URI, которые не находятся в базовом URI приложения.*

В следующем примере пользовательский класс расширяет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> для использования в качестве <xref:System.Net.Http.DelegatingHandler> для <xref:System.Net.Http.HttpClient>. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> настраивает этот обработчик для авторизации исходящих HTTP-запросов с помощью маркера доступа. Маркер доступа прикрепляется, только если по крайней мере один из разрешенных URL-адресов является базовым для URI запроса (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).

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

В `Program.Main` (`Program.cs`) `CustomAuthorizationMessageHandler` регистрируется как служба с ограниченной областью действия и настраивается как <xref:System.Net.Http.DelegatingHandler> для исходящих экземпляров <xref:System.Net.Http.HttpResponseMessage>, созданных с помощью именованного <xref:System.Net.Http.HttpClient>.

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.

Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch). Когда клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)), при выполнении запросов к интерфейсу API сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа. Если URI запроса является относительным URI, как показано в следующем примере (`ExampleAPIMethod`), он объединяется с <xref:System.Net.Http.HttpClient.BaseAddress>, когда клиентское приложение выполняет запрос.

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

### <a name="configure-authorizationmessagehandler"></a>Настройка `AuthorizationMessageHandler`

Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно настроить разрешенные URL-адреса, области и URL-адреса возврата с помощью метода <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> настраивает обработчик для авторизации исходящих HTTP-запросов с помощью маркера доступа. Маркер доступа прикрепляется, только если по крайней мере один из разрешенных URL-адресов является базовым для URI запроса (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>). Если URI запроса является относительным URI, он объединяется с <xref:System.Net.Http.HttpClient.BaseAddress>.

В следующем примере обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивает объект <xref:System.Net.Http.HttpClient> в `Program.Main` (`Program.cs`):

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

Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> назначается по умолчанию следующим образом.

* свойству <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`);
* URL-адресу массива `authorizedUrls`.

## <a name="typed-httpclient"></a>Типизированный `HttpClient`

Можно определить типизированный клиент, который будет обрабатывать все аспекты, связанные с HTTP и получением маркеров, в пределах одного класса.

`WeatherForecastClient.cs`:

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `using static BlazorSample.Data;`).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.

Компонент `FetchData` (`Pages/FetchData.razor`):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Настройка обработчика `HttpClient`

Обработчик можно дополнительно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> назначается по умолчанию следующим образом.

* свойству <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`);
* URL-адресу массива `authorizedUrls`.

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию

Если приложение Blazor WebAssembly обычно использует защищенный клиент <xref:System.Net.Http.HttpClient> по умолчанию, приложение также может выполнять неавторизованные или неаутентифицированные запросы к веб-API путем настройки именованного клиента <xref:System.Net.Http.HttpClient>:

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

Для приложения Blazor, основанного на размещенном шаблоне проекта Blazor WebAssembly, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) назначается <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> по умолчанию.

Приведенная выше регистрация является дополнением к существующей регистрации защищенного клиента <xref:System.Net.Http.HttpClient> по умолчанию.

Компонент создает клиент <xref:System.Net.Http.HttpClient> на основе <xref:System.Net.Http.IHttpClientFactory> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)) для выполнения не прошедших проверку или неавторизованных запросов:

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
> Контроллер в интерфейсе API сервера (`WeatherForecastNoAuthenticationController` в предыдущем примере) не помечен [атрибутом `[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).

Решение о том, следует ли использовать безопасный клиент или небезопасный клиент в качестве экземпляра <xref:System.Net.Http.HttpClient> по умолчанию, принимает разработчик. При принятии такого решения рекомендуется учесть соотношение неавторизованных и аутентифицированных конечных точек, к которым обращается приложение. Если приложение отправляет большинство запросов к безопасным конечным точкам API, используйте аутентифицированный экземпляр <xref:System.Net.Http.HttpClient> в качестве значения по умолчанию. В противном случае в качестве значения по умолчанию зарегистрируйте неавторизованный экземпляр <xref:System.Net.Http.HttpClient>.

Альтернативный подход к использованию <xref:System.Net.Http.IHttpClientFactory> заключается в создании [типизированного клиента](#typed-httpclient) для неаутентифицированного доступа к анонимным конечным точкам.

## <a name="request-additional-access-tokens"></a>Запрос дополнительных маркеров доступа

Маркеры доступа можно получать вручную, вызывая `IAccessTokenProvider.RequestAccessToken`. В приведенном ниже примере приложение требует дополнительной области для клиента <xref:System.Net.Http.HttpClient> по умолчанию. В примере использования библиотеки проверки подлинности Майкрософт (MSAL) область настраивается с помощью `MsalProviderOptions`:

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

Заполнители `{CUSTOM SCOPE 1}` и `{CUSTOM SCOPE 2}` в предыдущем примере — это пользовательские области.

Метод `IAccessTokenProvider.RequestToken` предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с указанным набором областей.

В компоненте Razor:

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

Заполнители `{CUSTOM SCOPE 1}` и `{CUSTOM SCOPE 2}` в предыдущем примере — это пользовательские области.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> возвращает:

* `true` с пригодным для использования `token`.
* `false`, если маркер не получен.

## <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам независимо от источника (CORS)

При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS заголовок `Authorization` должен быть разрешен политикой CORS.

Следующая политика включает в себя настройку для следующих элементов:

* Источники запроса (`http://localhost:5000`, `https://localhost:5001`).
* Любой метод (команда).
* Заголовки `Content-Type` и `Authorization`. Чтобы разрешить пользовательский заголовок (например, `x-custom-header`), выведите список заголовков при вызове <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* Учетные данные, установленные клиентским кодом JavaScript (для свойства `credentials` задано значение `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Размещенное решение Blazor на основе размещенного шаблона проекта Blazor, использует тот же базовый адрес для клиентских и серверных приложений. Для <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> клиентского приложения задан универсальный код ресурса (URI) `builder.HostEnvironment.BaseAddress` по умолчанию. Конфигурация CORS **не** требуется в конфигурации по умолчанию размещенного приложения, созданного из размещенного шаблона проекта Blazor. Дополнительным клиентским приложениям, которые не размещаются в серверном проекте и не используют базовый адрес серверного приложения **требуется** конфигурация CORS в серверном проекте.

Дополнительные сведения см. в разделе <xref:security/cors> и компоненте "Тестер HTTP-запросов" примера приложения (`Components/HTTPRequestTester.razor`).

## <a name="handle-token-request-errors"></a>Обработка ошибок при выполнении запросов маркеров

Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью OpenID Connect (OIDC), состояние проверки подлинности сохраняется локально в приложении SPA и в поставщике Identity (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.

Маркеры, которые IP-адрес выдает для пользователя, обычно действительны недолго (как правило, около часа), поэтому клиентское приложение должно регулярно получать новые маркеры. В противном случае после истечения срока действия предоставленных маркеров будет выполнен выход пользователя из системы. В большинстве случаев клиенты OIDC могут подготавливать новые маркеры без повторного прохождения проверки подлинности пользователем благодаря сохранению состояния проверки подлинности или сеанса в поставщике удостоверений.

В некоторых случаях клиент не может получить маркер без участия пользователя, например, если по какой-либо причине пользователь явно вышел из поставщика удостоверений. Такая ситуация возникает, если пользователь переходит по адресу `https://login.microsoftonline.com` и выполняет выход из системы. В этих случаях приложению не становится известно о выходе пользователя из системы немедленно. Любой маркер, который имеется у клиента, может быть уже недействительным. Кроме того, клиент не может подготовить новый маркер без участия пользователя после истечения срока действия текущего маркера.

Такие сценарии нехарактерны для проверки подлинности на основе маркеров. Они проистекают из особенностей одностраничных приложений. Одностраничному приложению, использующему файлы cookie, также не удастся вызвать интерфейс API сервера в случае удаления файла cookie для проверки подлинности.

Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:

* Для подготовки нового маркера доступа с целью вызова API пользователю может потребоваться пройти проверку подлинности повторно.
* Даже если у клиента есть маркер, который представляется действительным, вызов сервера может завершиться ошибкой из-за того, что маркер был отозван пользователем.

Когда приложение запрашивает маркер, возможны два результата:

* Запрос выполняется успешно, и приложение получает действительный маркер.
* Запрос завершается ошибкой, и приложение должно повторно провести проверку подлинности пользователя, чтобы получить новый маркер.

При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления. Ниже приведены возможные подходы в порядке увеличения сложности.

* Сохраните текущее состояние страницы в хранилище сеансов. Во время [события жизненного цикла `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) проверьте, можно ли восстановить состояние, прежде чем продолжить.
* Добавьте параметр строки запроса и используйте его, чтобы сообщить приложению о необходимости восстановить ранее сохраненное состояние.
* Добавьте параметр строки запроса с уникальным идентификатором для сохранения данных в хранилище сеансов без риска конфликтов с другими элементами.

В приведенном ниже примере показано, как выполнить следующие задачи.

* сохранить состояние перед перенаправлением на страницу входа;
* восстановить предыдущее состояние после проверки подлинности с помощью параметра строки запроса.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Сохранение состояния приложения перед операцией проверки подлинности

Во время операции проверки подлинности существуют случаи, когда необходимо сохранить состояние приложения, прежде чем в браузере будет выполнено перенаправление в поставщик удостоверений. Например, такая потребность может возникнуть, когда вы используете контейнер состояния и хотите восстановить состояние после успешной проверки подлинности. Вы можете использовать пользовательский объект состояния проверки подлинности для сохранения состояния приложения или ссылки на него и восстанавливать это состояние после успешного завершения проверки подлинности. Такой подход демонстрируется в приведенном ниже примере.

В приложении создается класс контейнера состояния со свойствами для хранения значений состояния приложения. В приведенном ниже примере контейнер используется для хранения значения счетчика для компонента `Counter` шаблона проекта по умолчанию (`Pages/Counter.razor`). Методы сериализации и десериализации контейнера основаны на <xref:System.Text.Json>.

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

Компонент `Counter` использует контейнер состояния для хранения значения `currentCount` за пределами компонента:

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

Создайте `ApplicationAuthenticationState` на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>. Укажите свойство `Id`, которое служит идентификатором для локально хранящегося состояния.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

Компонент `Authentication` (`Pages/Authentication.razor`) сохраняет и восстанавливает состояние приложения, используя локальное хранилище сеансов, с помощью методов сериализации и десериализации `StateContainer` (`GetStateForLocalStorage` и `SetStateFromLocalStorage`):

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

В этом примере для проверки подлинности используется Azure Active Directory (AAD). В `Program.Main` (`Program.cs`):

* `ApplicationAuthenticationState` настраивается с типом `RemoteAuthenticationState` из библиотеки проверки подлинности Майкрософт (MSAL).
* Контейнер состояния регистрируется в контейнере службы.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Настройка маршрутов приложения

Для представления различных состояний проверки подлинности библиотека [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) по умолчанию использует маршруты, приведенные в таблице ниже.

| Маршрут                            | Цель |
| -------------------------------- | ------- |
| `authentication/login`           | Активирует операцию входа. |
| `authentication/login-callback`  | Обрабатывает результат любой операции входа. |
| `authentication/login-failed`    | Отображает сообщения об ошибках при сбое операции входа по какой-либо причине. |
| `authentication/logout`          | Активирует операцию выхода. |
| `authentication/logout-callback` | Обрабатывает результат любой операции выхода. |
| `authentication/logout-failed`   | Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине. |
| `authentication/logged-out`      | Указывает, что пользователь успешно выполнил выход. |
| `authentication/profile`         | Активирует операцию изменения профиля пользователя. |
| `authentication/register`        | Активирует операцию регистрации нового пользователя. |

Маршруты, представленные в предыдущей таблице, можно настраивать с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>. При настройке параметров для предоставления пользовательских маршрутов убедитесь в том, что у приложения есть маршрут для обработки каждого пути.

В приведенном ниже примере все пути имеют префикс `/security`.

Компонент `Authentication` (`Pages/Authentication.razor`):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Если требуются совершенно разные пути, задайте маршруты, как описано выше, и обработайте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра действия:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

При необходимости можно разделить пользовательский интерфейс на несколько страниц.

## <a name="customize-the-authentication-user-interface"></a>Настройка пользовательского интерфейса проверки подлинности

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> содержит набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности. Каждое состояние можно настроить, передав пользовательский объект <xref:Microsoft.AspNetCore.Components.RenderFragment>. Чтобы настроить текст, отображаемый во время первоначального входа в систему, можно изменить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> указанным ниже образом.

Компонент `Authentication` (`Pages/Authentication.razor`):

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

В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> есть фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в приведенной ниже таблице.

| Маршрут                            | Fragment                |
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

## <a name="customize-the-user"></a>Настройка пользователя

Пользователей, привязанных к приложению, можно настраивать.

### <a name="customize-the-user-with-a-payload-claim"></a>Настройка пользователя с утверждением полезной нагрузки

В приведенном ниже примере пользователи приложения, прошедшие проверку подлинности, получают утверждение `amr` для каждого из методов проверки подлинности. Утверждение `amr` указывает способ проверки подлинности субъекта токена в [утверждениях полезной нагрузки](/azure/active-directory/develop/access-tokens#the-amr-claim) Microsoft Identity Platform версии 1.0. В примере используется настраиваемый класс учетной записи пользователя на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.

Создайте класс, расширяющий класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>. В приведенном ниже примере свойству `AuthenticationMethod` присваивается пользовательский массив значений свойств JSON `amr`. `AuthenticationMethod` заполняется платформой автоматически при проверке подлинности пользователя.

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Создайте фабрику, расширяющую <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> для создания утверждений на основе методов проверки подлинности пользователя, хранящихся в `CustomUserAccount.AuthenticationMethod`:

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

Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности. Допустима любая из следующих регистраций:

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a>Группы безопасности и роли AAD с настраиваемым классом учетной записи пользователя

Дополнительный пример, который работает с группами безопасности AAD, ролями администратора AAD и настраиваемым классом учетной записи пользователя, см. в разделе <xref:blazor/security/webassembly/aad-groups-roles>.

## <a name="support-prerendering-with-authentication"></a>Поддержка предварительной отрисовки с помощью проверки подлинности

Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:

* предварительно отрисовывает пути, не требующие авторизации;
* не выполняет предварительную отрисовку путей, требующих авторизации.

В классе  *приложения `Client`* `Program` (`Program.cs`) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):

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

В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:

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

В методе `Startup.Configure` серверного приложения замените [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) на [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

В серверном приложении создайте папку `Pages`, если она отсутствует. В папке серверного приложения `Pages` создайте страницу `_Host.cshtml`. Вставьте содержимое из файла `wwwroot/index.html` приложения *`Client`* в файл `Pages/_Host.cshtml`. Обновите содержимое файла:

::: moniker range=">= aspnetcore-5.0"

* Добавьте `@page "_Host"` в начало файла.
* Замените тег `<div id="app">Loading...</div>` следующим:

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
  
  Заполнитель `{CLIENT APP ASSEMBLY NAME}` в предыдущем примере — это имя сборки клиентского приложения (например, `BlazorSample.Client`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Добавьте `@page "_Host"` в начало файла.
* Замените тег `<app>Loading...</app>` следующим:

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
  
  Заполнитель `{CLIENT APP ASSEMBLY NAME}` в предыдущем примере — это имя сборки клиентского приложения (например, `BlazorSample.Client`).

::: moniker-end
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Варианты для размещенных приложений и сторонних поставщиков входа

При аутентификации и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов аутентификации пользователя. Выбор варианта зависит от вашего сценария.

Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Проверка подлинности пользователей для вызова только защищенных сторонних API

Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 В этом сценарии:

* Сервер, на котором размещено приложение, не имеет особого значения.
* Невозможно обеспечить защиту API на сервере.
* Приложение может вызывать только защищенные сторонние интерфейсы API.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API

Настройте Identity с помощью стороннего поставщика входа. Получите токены, необходимые для доступа к сторонним API, и сохраните их.

При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности. На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Использование токена доступа сервера для получения стороннего токена доступа

С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера. Затем воспользуйтесь сторонним маркером доступа для вызова ресурсов стороннего API непосредственно из Identity в клиенте.

Этот вариант использовать не рекомендуется. Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента. С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента. Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.

* Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.
* Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Отправка вызовов API с API клиента на API сервера для вызова сторонних API

Отправьте вызов API с API клиента на API сервера. На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.

Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.

* Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.
* Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Использование конечных точек OpenID Connect (OIDC) версии 2.0

В библиотеке проверки подлинности и шаблонах проекта Blazor используются конечные точки OpenID Connect (OIDC) версии 1.0. Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> маркера носителя JWT. В следующем примере для AAD настраивается версия 2.0 путем добавления сегмента `v2.0` к свойству <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую. Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>, либо в файле параметров приложения (`appsettings.json`) с помощью ключа `Authority`.

Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0. Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)

## <a name="configure-and-use-grpc-in-components"></a>Настройка и использование gRPC в компонентах

Чтобы настроить приложение Blazor WebAssembly для использования [платформы ASP.NET Core gRPC](xref:grpc/index), выполните указанные ниже действия.

* Включите gRPC-Web на сервере. Для получения дополнительной информации см. <xref:grpc/browser>.
* Зарегистрируйте службы gRPC для обработчика сообщений приложения. В следующем примере обработчик сообщений авторизации приложения настраивается для использования [службы `GreeterClient` из учебника по gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`). Поместите файл `.proto` в проект `Shared` размещенного решения Blazor.

Компонент в клиентском приложении может выполнять вызовы gRPC с помощью клиента gRPC (`Pages/Grpc.razor`):

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`). Чтобы использовать свойство `Status.DebugException`, используйте [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.30.0 или более поздней.

Для получения дополнительной информации см. <xref:grpc/browser>.

## <a name="build-a-custom-version-of-the-authenticationmsal-javascript-library"></a>Создание пользовательской версии библиотеки Authentication.MSAL для JavaScript

Если приложению требуется пользовательская версия [библиотеки проверки подлинности Майкрософт для JavaScript (MSAL.js)](https://www.npmjs.com/package/@azure/msal-browser):

1. Убедитесь, что в системе установлен последний пакет SDK для разработчиков .NET, или получите и установите последнюю версию пакета SDK для разработчиков, как описано [ здесь](https://github.com/dotnet/installer#installers-and-binaries). Для этого сценария настройка внутренних веб-каналов NuGet не требуется.
1. Настройте репозиторий GitHub `dotnet/aspnetcore` для разработки, как описано [здесь](https://github.com/dotnet/aspnetcore/blob/main/docs/BuildFromSource.md). Создайте вилку и клонируйте или скачайте ZIP-архив [репозитория dotnet/aspnetcore в GitHub](https://github.com/dotnet/aspnetcore).
1. Откройте файл `src/Components/WebAssembly/Authentication.Msal/src/Interop/package.json` и задайте нужную версию `@azure/msal-browser`. Чтобы получить список выпущенных версий, посетите веб-сайт [npm `@azure/msal-browser`](https://www.npmjs.com/package/@azure/msal-browser) и выберите вкладку **Versions** (Версии).
1. Создайте проект `Authentication.Msal` в папке `src/Components/WebAssembly/Authentication.Msal/src` с помощью команды `yarn build` в командной оболочке.
1. Если приложение использует [сжатые ресурсы (Brotli/Gzip)](xref:blazor/host-and-deploy/webassembly#compression), необходимо сжать файл `Interop/dist/Release/AuthenticationService.js`.
1. Скопируйте файл `AuthenticationService.js` и сжатые версии (`.br`/`.gz`) файла, если они созданы, из папки `Interop/dist/Release` в папку `publish/wwwroot/_content/Microsoft.Authentication.WebAssembly.Msal` приложения в опубликованных ресурсах приложения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/security/webassembly/graph-api>
* [`HttpClient` и `HttpRequestMessage` с параметрами запроса API Fetch](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
