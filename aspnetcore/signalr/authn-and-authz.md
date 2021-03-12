---
title: Проверка подлинности и авторизация в ASP.NET Core SignalR
author: bradygaster
description: Узнайте, как использовать проверку подлинности и авторизацию в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
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
uid: signalr/authn-and-authz
ms.openlocfilehash: 9a3102e4451bbc5cd9ff15e88bebd4e4f2c115f4
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588104"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="03ae8-103">Проверка подлинности и авторизация в ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="03ae8-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="03ae8-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="03ae8-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="03ae8-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="03ae8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="03ae8-106">Проверка подлинности пользователей, подключающихся к SignalR концентратору</span><span class="sxs-lookup"><span data-stu-id="03ae8-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="03ae8-107">SignalR можно использовать с [проверкой подлинности ASP.NET Core](xref:security/authentication/identity) , чтобы связать пользователя с каждым соединением.</span><span class="sxs-lookup"><span data-stu-id="03ae8-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="03ae8-108">В центре данные проверки подлинности можно получить из свойства [хубконнектионконтекст. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="03ae8-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="03ae8-109">Проверка подлинности позволяет концентратору вызывать методы для всех соединений, связанных с пользователем.</span><span class="sxs-lookup"><span data-stu-id="03ae8-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="03ae8-110">Дополнительные сведения см. [в разделе Управление пользователями и группами SignalR в ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="03ae8-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="03ae8-111">Несколько подключений могут быть связаны с одним пользователем.</span><span class="sxs-lookup"><span data-stu-id="03ae8-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="03ae8-112">Ниже приведен пример `Startup.Configure` использования SignalR и ASP.NET Core проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="03ae8-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="03ae8-113">Порядок, в котором регистрируется SignalR и ASP.NET Core по промежуточного слоя проверки подлинности имеет значение.</span><span class="sxs-lookup"><span data-stu-id="03ae8-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="03ae8-114">Всегда вызывайте метод `UseAuthentication` перед `UseSignalR` , чтобы SignalR иметь пользователя в `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="03ae8-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="03ae8-115">Cookie аутентификация</span><span class="sxs-lookup"><span data-stu-id="03ae8-115">Cookie authentication</span></span>

<span data-ttu-id="03ae8-116">В приложении, основанном на браузере, cookie Проверка подлинности позволяет существующим учетным данным пользователя автоматически передаваться на SignalR подключения.</span><span class="sxs-lookup"><span data-stu-id="03ae8-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="03ae8-117">При использовании клиента браузера дополнительная настройка не требуется.</span><span class="sxs-lookup"><span data-stu-id="03ae8-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="03ae8-118">Если пользователь вошел в приложение, SignalR подключение автоматически наследует эту проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="03ae8-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="03ae8-119">Cookies — это способ отправки маркеров доступа в браузере, но клиенты, не являющиеся браузерами, могут их отправить.</span><span class="sxs-lookup"><span data-stu-id="03ae8-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="03ae8-120">При использовании [клиента .NET](xref:signalr/dotnet-client) `Cookies` свойство можно настроить в `.WithUrl` вызове, чтобы предоставить cookie .</span><span class="sxs-lookup"><span data-stu-id="03ae8-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="03ae8-121">Однако для использования cookie проверки подлинности от клиента .NET приложение должно предоставить API для обмена данными проверки подлинности для cookie .</span><span class="sxs-lookup"><span data-stu-id="03ae8-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="03ae8-122">Проверка подлинности маркера носителя</span><span class="sxs-lookup"><span data-stu-id="03ae8-122">Bearer token authentication</span></span>

<span data-ttu-id="03ae8-123">Клиент может предоставить маркер доступа вместо использования cookie .</span><span class="sxs-lookup"><span data-stu-id="03ae8-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="03ae8-124">Сервер проверяет маркер и использует его для обнаружения пользователя.</span><span class="sxs-lookup"><span data-stu-id="03ae8-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="03ae8-125">Эта проверка выполняется только при установленном соединении.</span><span class="sxs-lookup"><span data-stu-id="03ae8-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="03ae8-126">Во время жизни соединения сервер не выполняет автоматическую повторную проверку, чтобы проверить отзыв маркера.</span><span class="sxs-lookup"><span data-stu-id="03ae8-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="03ae8-127">В клиенте JavaScript маркер можно указать с помощью параметра [акцесстокенфактори](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="03ae8-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="03ae8-128">В клиенте .NET существует аналогичное свойство [акцесстокенпровидер](xref:signalr/configuration#configure-bearer-authentication) , которое можно использовать для настройки маркера:</span><span class="sxs-lookup"><span data-stu-id="03ae8-128">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="03ae8-129">Предоставляемая функция токена доступа вызывается перед **каждым** HTTP-запросом, выполненным SignalR .</span><span class="sxs-lookup"><span data-stu-id="03ae8-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="03ae8-130">Если необходимо обновить токен, чтобы подключение было активно (так как оно может истечь во время подключения), сделайте это в этой функции и возвратите обновленный маркер.</span><span class="sxs-lookup"><span data-stu-id="03ae8-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="03ae8-131">В стандартных веб-API токены носителя отправляются в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="03ae8-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="03ae8-132">Однако SignalR не может задать эти заголовки в браузерах при использовании некоторых транспортов.</span><span class="sxs-lookup"><span data-stu-id="03ae8-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="03ae8-133">При использовании WebSockets и Server-Sent событий маркер передается как параметр строки запроса.</span><span class="sxs-lookup"><span data-stu-id="03ae8-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> 

#### <a name="built-in-jwt-authentication"></a><span data-ttu-id="03ae8-134">Встроенная проверка подлинности JWT</span><span class="sxs-lookup"><span data-stu-id="03ae8-134">Built-in JWT authentication</span></span>

<span data-ttu-id="03ae8-135">На сервере проверка подлинности токена носителя настраивается с помощью по [промежуточного слоя JWT Bearer](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span><span class="sxs-lookup"><span data-stu-id="03ae8-135">On the server, bearer token authentication is configured using the [JWT Bearer middleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="03ae8-136">Строка запроса используется в браузерах при подключении с помощью WebSockets и событий Server-Sent из-за ограничений API браузера.</span><span class="sxs-lookup"><span data-stu-id="03ae8-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="03ae8-137">При использовании HTTPS значения строки запроса защищаются с помощью подключения TLS.</span><span class="sxs-lookup"><span data-stu-id="03ae8-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="03ae8-138">Однако многие серверы заносить в журнал значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="03ae8-138">However, many servers log query string values.</span></span> <span data-ttu-id="03ae8-139">Дополнительные сведения см. [в разделе вопросы безопасности в SignalR ASP.NET Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="03ae8-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> <span data-ttu-id="03ae8-140">SignalR использует заголовки для передачи токенов в средах, которые их поддерживают (например, клиенты .NET и Java).</span><span class="sxs-lookup"><span data-stu-id="03ae8-140">SignalR uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

#### <a name="identity-server-jwt-authentication"></a><span data-ttu-id="03ae8-141">Identity Проверка подлинности сервера JWT</span><span class="sxs-lookup"><span data-stu-id="03ae8-141">Identity Server JWT authentication</span></span>

<span data-ttu-id="03ae8-142">При использовании Identity сервера добавьте <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> службу в проект:</span><span class="sxs-lookup"><span data-stu-id="03ae8-142">When using Identity Server, add a <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service to the project:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

<span data-ttu-id="03ae8-143">Зарегистрируйте службу в `Startup.ConfigureServices` после добавления служб для проверки подлинности ( <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ) и обработчика проверки подлинности для Identity сервера ( <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ):</span><span class="sxs-lookup"><span data-stu-id="03ae8-143">Register the service in `Startup.ConfigureServices` after adding services for authentication (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) and the authentication handler for Identity Server (<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>):</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="03ae8-144">Cookieи токены носителя</span><span class="sxs-lookup"><span data-stu-id="03ae8-144">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="03ae8-145">Cookieотносятся только к браузерам.</span><span class="sxs-lookup"><span data-stu-id="03ae8-145">Cookies are specific to browsers.</span></span> <span data-ttu-id="03ae8-146">Их отправка из других типов клиентов повышает сложность по сравнению с отправкой токенов носителя.</span><span class="sxs-lookup"><span data-stu-id="03ae8-146">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="03ae8-147">Следовательно, cookie Проверка подлинности не рекомендуется, если приложению требуется только проверка подлинности пользователей только от клиента браузера.</span><span class="sxs-lookup"><span data-stu-id="03ae8-147">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="03ae8-148">Проверка подлинности маркера носителя является рекомендуемым подходом при использовании клиентов, отличных от клиента браузера.</span><span class="sxs-lookup"><span data-stu-id="03ae8-148">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="03ae8-149">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="03ae8-149">Windows authentication</span></span>

<span data-ttu-id="03ae8-150">Если в приложении настроена [Проверка подлинности Windows](xref:security/authentication/windowsauth) , SignalR может использовать это удостоверение для защиты концентраторов.</span><span class="sxs-lookup"><span data-stu-id="03ae8-150">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="03ae8-151">Однако для отправки сообщений отдельным пользователям необходимо добавить настраиваемого поставщика ИДЕНТИФИКАТОРов пользователей.</span><span class="sxs-lookup"><span data-stu-id="03ae8-151">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="03ae8-152">Система проверки подлинности Windows не предоставляет утверждение "идентификатор имени".</span><span class="sxs-lookup"><span data-stu-id="03ae8-152">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="03ae8-153">SignalR использует утверждение для определения имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="03ae8-153">SignalR uses the claim to determine the user name.</span></span>

<span data-ttu-id="03ae8-154">Добавьте новый класс, который реализует `IUserIdProvider` и извлекает одно из утверждений пользователя для использования в качестве идентификатора.</span><span class="sxs-lookup"><span data-stu-id="03ae8-154">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="03ae8-155">Например, чтобы использовать утверждение "Name" (имя пользователя Windows в форме `[Domain]\[Username]` ), создайте следующий класс:</span><span class="sxs-lookup"><span data-stu-id="03ae8-155">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="03ae8-156">Вместо `ClaimTypes.Name` можно использовать любое значение из `User` (например, идентификатор SID Windows и т. д.).</span><span class="sxs-lookup"><span data-stu-id="03ae8-156">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="03ae8-157">Выбранное значение должно быть уникальным для всех пользователей в системе.</span><span class="sxs-lookup"><span data-stu-id="03ae8-157">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="03ae8-158">В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="03ae8-158">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="03ae8-159">Зарегистрируйте этот компонент в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="03ae8-159">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="03ae8-160">В клиенте .NET проверка подлинности Windows должна быть включена путем установки свойства [уседефаулткредентиалс](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="03ae8-160">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="03ae8-161">Проверка подлинности Windows поддерживается в Internet Explorer и Microsoft погранично, но не во всех браузерах.</span><span class="sxs-lookup"><span data-stu-id="03ae8-161">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="03ae8-162">Например, в Chrome и Safari попытка использовать проверку подлинности Windows и WebSockets завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="03ae8-162">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="03ae8-163">При сбое проверки подлинности Windows клиент пытается вернуться к другим транспортам, которые могут работать.</span><span class="sxs-lookup"><span data-stu-id="03ae8-163">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="03ae8-164">Использование утверждений для настройки обработки удостоверений</span><span class="sxs-lookup"><span data-stu-id="03ae8-164">Use claims to customize identity handling</span></span>

<span data-ttu-id="03ae8-165">Приложение, выполняющее проверку подлинности пользователей, может наследовать SignalR идентификаторы пользователей от утверждений пользователей.</span><span class="sxs-lookup"><span data-stu-id="03ae8-165">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="03ae8-166">Чтобы указать SignalR , как создаются идентификаторы пользователей, реализуйте `IUserIdProvider` и зарегистрируйте реализацию.</span><span class="sxs-lookup"><span data-stu-id="03ae8-166">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="03ae8-167">В примере кода показано использование утверждений для выбора адреса электронной почты пользователя в качестве идентифицирующего свойства.</span><span class="sxs-lookup"><span data-stu-id="03ae8-167">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="03ae8-168">Выбранное значение должно быть уникальным для всех пользователей в системе.</span><span class="sxs-lookup"><span data-stu-id="03ae8-168">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="03ae8-169">В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="03ae8-169">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="03ae8-170">При регистрации учетной записи в `ClaimsTypes.Email` базу данных удостоверений ASP.NET добавляется утверждение с типом.</span><span class="sxs-lookup"><span data-stu-id="03ae8-170">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="03ae8-171">Зарегистрируйте этот компонент в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03ae8-171">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="03ae8-172">Авторизация пользователей для доступа к концентраторам и методам концентратора</span><span class="sxs-lookup"><span data-stu-id="03ae8-172">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="03ae8-173">По умолчанию все методы в концентраторе могут вызываться пользователем, не прошедшим проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="03ae8-173">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="03ae8-174">Чтобы требовать проверку подлинности, примените к концентратору атрибут [авторизации](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) :</span><span class="sxs-lookup"><span data-stu-id="03ae8-174">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="03ae8-175">Можно использовать аргументы конструктора и свойства атрибута `[Authorize]`, чтобы ограничить доступ только пользователями, соответствующими определенным [политикам авторизации](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="03ae8-175">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="03ae8-176">Например, при наличии пользовательской политики авторизации `MyAuthorizationPolicy` можно убедиться, что только пользователи, соответствующие этой политике, могут получить доступ к концентратору с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="03ae8-176">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="03ae8-177">Отдельные методы концентратора могут `[Authorize]` также применять атрибут.</span><span class="sxs-lookup"><span data-stu-id="03ae8-177">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="03ae8-178">Если текущий пользователь не соответствует политике, примененной к методу, вызывающему объекту будет возвращена ошибка.</span><span class="sxs-lookup"><span data-stu-id="03ae8-178">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="03ae8-179">Использование обработчиков авторизации для настройки авторизации метода концентратора</span><span class="sxs-lookup"><span data-stu-id="03ae8-179">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="03ae8-180">SignalR предоставляет пользовательский ресурс для обработчиков авторизации, когда методу концентратора требуется авторизация.</span><span class="sxs-lookup"><span data-stu-id="03ae8-180">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="03ae8-181">Ресурс является экземпляром `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="03ae8-181">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="03ae8-182">`HubInvocationContext`Включает `HubCallerContext` , имя вызываемого метода концентратора и аргументы для метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="03ae8-182">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="03ae8-183">Рассмотрим пример комнаты чатов, позволяющей нескольким организациям входить в систему через Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="03ae8-183">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="03ae8-184">Любой пользователь, у которого есть учетная запись Майкрософт, может войти в чат, но только члены владеющей Организации должны иметь возможность запрещать пользователям или просматривать историю разговора пользователей.</span><span class="sxs-lookup"><span data-stu-id="03ae8-184">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="03ae8-185">Кроме того, может потребоваться ограничить некоторые функции от определенных пользователей.</span><span class="sxs-lookup"><span data-stu-id="03ae8-185">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="03ae8-186">Использование обновленных функций в ASP.NET Core 3,0 — это вполне возможно.</span><span class="sxs-lookup"><span data-stu-id="03ae8-186">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="03ae8-187">Обратите внимание, что `DomainRestrictedRequirement` служит в качестве пользовательского `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="03ae8-187">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="03ae8-188">Теперь, когда `HubInvocationContext` передается параметр ресурса, внутренняя логика может проверить контекст, в котором вызывается концентратор, и принимать решения о том, чтобы разрешить пользователю выполнять отдельные методы концентратора.</span><span class="sxs-lookup"><span data-stu-id="03ae8-188">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="03ae8-189">В `Startup.ConfigureServices` добавьте новую политику, предоставив настраиваемое требование в `DomainRestrictedRequirement` качестве параметра для создания `DomainRestricted` политики.</span><span class="sxs-lookup"><span data-stu-id="03ae8-189">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="03ae8-190">В предыдущем примере `DomainRestrictedRequirement` класс является `IAuthorizationRequirement` и, и его собственным `AuthorizationHandler` для этого требования.</span><span class="sxs-lookup"><span data-stu-id="03ae8-190">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="03ae8-191">Можно разделить эти два компонента на отдельные классы для разделения проблем.</span><span class="sxs-lookup"><span data-stu-id="03ae8-191">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="03ae8-192">Преимуществом этого примера является отсутствие необходимости внедрять `AuthorizationHandler` во время запуска, так как требование и обработчик одинаковы.</span><span class="sxs-lookup"><span data-stu-id="03ae8-192">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="03ae8-193">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="03ae8-193">Additional resources</span></span>

* [<span data-ttu-id="03ae8-194">Проверка подлинности токена носителя в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03ae8-194">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="03ae8-195">Авторизация на основе ресурсов</span><span class="sxs-lookup"><span data-stu-id="03ae8-195">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
