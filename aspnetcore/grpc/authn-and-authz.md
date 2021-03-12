---
title: Проверка подлинности и авторизация в gRPC для ASP.NET Core
author: jamesnk
description: Узнайте, как использовать проверку подлинности и авторизацию в gRPC для ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 5120964459a81ef2d668877bb08ecde512c2389d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588000"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="db93d-103">Проверка подлинности и авторизация в gRPC для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db93d-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="db93d-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="db93d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="db93d-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="db93d-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="db93d-106">Проверка подлинности пользователей, вызывающих службу gRPC</span><span class="sxs-lookup"><span data-stu-id="db93d-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="db93d-107">gRPC можно использовать с [проверкой подлинности ASP.NET Core](xref:security/authentication/identity), чтобы связать пользователя с каждым вызовом.</span><span class="sxs-lookup"><span data-stu-id="db93d-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="db93d-108">Ниже приведен пример `Startup.Configure`, в котором используется проверка подлинности gRPC и ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="db93d-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="db93d-109">Порядок, в котором регистрируется ПО промежуточного слоя ASP.NET Core для проверки подлинности, имеет значение.</span><span class="sxs-lookup"><span data-stu-id="db93d-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="db93d-110">Всегда вызывайте `UseAuthentication` и `UseAuthorization` после `UseRouting` и до `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="db93d-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="db93d-111">Механизм проверки подлинности, используемый приложением во время вызова, необходимо настроить.</span><span class="sxs-lookup"><span data-stu-id="db93d-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="db93d-112">Конфигурация проверки подлинности добавляется в `Startup.ConfigureServices` и будет отличаться в зависимости от используемого в приложении механизма проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="db93d-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="db93d-113">Примеры защиты приложений ASP.NET Core см. в разделе [Примеры проверки подлинности](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="db93d-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="db93d-114">После настройки проверки подлинности методы службы gRPC могут получить доступ к пользователю через `ServerCallContext`.</span><span class="sxs-lookup"><span data-stu-id="db93d-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="db93d-115">Проверка подлинности маркера носителя</span><span class="sxs-lookup"><span data-stu-id="db93d-115">Bearer token authentication</span></span>

<span data-ttu-id="db93d-116">Клиент может предоставить маркер доступа для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="db93d-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="db93d-117">Сервер проверяет маркер и использует его для обнаружения пользователя.</span><span class="sxs-lookup"><span data-stu-id="db93d-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="db93d-118">На сервере проверка подлинности маркера носителя настраивается с помощью [По промежуточного слоя JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="db93d-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="db93d-119">В клиенте .NET gRPC маркер можно отправить с помощью вызовов с использованием коллекции `Metadata`.</span><span class="sxs-lookup"><span data-stu-id="db93d-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="db93d-120">Записи в коллекции `Metadata` отправляются с помощью вызова gRPC в виде заголовков HTTP:</span><span class="sxs-lookup"><span data-stu-id="db93d-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="db93d-121">Настройка `ChannelCredentials` в канале — это альтернативный способ отправки маркера службе с вызовами gRPC.</span><span class="sxs-lookup"><span data-stu-id="db93d-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="db93d-122">Класс `ChannelCredentials` может содержать класс `CallCredentials`, что позволяет автоматически задавать `Metadata`.</span><span class="sxs-lookup"><span data-stu-id="db93d-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="db93d-123">Класс `CallCredentials` запускается каждый раз при вызове gRPC, что позволяет избежать необходимости писать код в нескольких местах для самостоятельной передачи маркера.</span><span class="sxs-lookup"><span data-stu-id="db93d-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="db93d-124">Обратите внимание, что класс `CallCredentials` применяется только в том случае, если канал защищен с помощью TLS.</span><span class="sxs-lookup"><span data-stu-id="db93d-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="db93d-125">Класс `CallCredentials` не применяется к незащищенным каналам без поддержки TLS.</span><span class="sxs-lookup"><span data-stu-id="db93d-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="db93d-126">Учетные данные в следующем примере настраивают канал для отправки маркера при каждом вызове gRPC:</span><span class="sxs-lookup"><span data-stu-id="db93d-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="db93d-127">Аутентификация на основе сертификата клиента</span><span class="sxs-lookup"><span data-stu-id="db93d-127">Client certificate authentication</span></span>

<span data-ttu-id="db93d-128">Клиент может также предоставить сертификат клиента для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="db93d-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="db93d-129">[Проверка подлинности по сертификату](https://tools.ietf.org/html/rfc5246#section-7.4.4) происходит на уровне TLS, задолго до его попадания в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db93d-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="db93d-130">Когда запрос попадает в ASP.NET Core, [пакет проверки подлинности сертификата клиента](xref:security/authentication/certauth) позволяет разрешить сертификат в `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="db93d-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="db93d-131">Сервер должен быть настроен для принятия сертификатов клиентов.</span><span class="sxs-lookup"><span data-stu-id="db93d-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="db93d-132">Сведения о принятии сертификатов клиента в Kestrel, IIS и Azure см. в статье <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span><span class="sxs-lookup"><span data-stu-id="db93d-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="db93d-133">В клиенте .NET gRPC сертификат клиента добавляется в `HttpClientHandler`, который затем используется для создания клиента gRPC:</span><span class="sxs-lookup"><span data-stu-id="db93d-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="db93d-134">Другие механизмы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="db93d-134">Other authentication mechanisms</span></span>

<span data-ttu-id="db93d-135">Многие механизмы проверки подлинности вASP.NET Core работают с gRPC:</span><span class="sxs-lookup"><span data-stu-id="db93d-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="db93d-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="db93d-136">Azure Active Directory</span></span>
* <span data-ttu-id="db93d-137">Сертификат клиента</span><span class="sxs-lookup"><span data-stu-id="db93d-137">Client Certificate</span></span>
* <span data-ttu-id="db93d-138">IdentityСервер</span><span class="sxs-lookup"><span data-stu-id="db93d-138">IdentityServer</span></span>
* <span data-ttu-id="db93d-139">Маркер JWT</span><span class="sxs-lookup"><span data-stu-id="db93d-139">JWT Token</span></span>
* <span data-ttu-id="db93d-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="db93d-140">OAuth 2.0</span></span>
* <span data-ttu-id="db93d-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="db93d-141">OpenID Connect</span></span>
* <span data-ttu-id="db93d-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="db93d-142">WS-Federation</span></span>

<span data-ttu-id="db93d-143">Дополнительные сведения о настройке проверки подлинности на сервере см. в разделе [Проверка подлинности в ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="db93d-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="db93d-144">Настройка клиента gRPC для использования проверки подлинности будет зависеть от используемого механизма проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="db93d-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="db93d-145">В предыдущих примерах маркера носителя и сертификата клиента показано несколько способов настройки клиента gRPC для отправки метаданных проверки подлинности с помощью вызовов gRPC:</span><span class="sxs-lookup"><span data-stu-id="db93d-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="db93d-146">Строго типизированные клиенты gRPC используют `HttpClient` внутренне.</span><span class="sxs-lookup"><span data-stu-id="db93d-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="db93d-147">Проверку подлинности можно настроить с помощью [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) или путем добавления пользовательских экземпляров [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) в `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="db93d-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="db93d-148">Каждый вызов gRPC имеет необязательный аргумент `CallOptions`.</span><span class="sxs-lookup"><span data-stu-id="db93d-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="db93d-149">Пользовательские заголовки можно отправлять с помощью коллекции заголовков параметра.</span><span class="sxs-lookup"><span data-stu-id="db93d-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="db93d-150">Проверка подлинности Windows (NTLM/Kerberos/Negotiate) не может использоваться с gRPC.</span><span class="sxs-lookup"><span data-stu-id="db93d-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="db93d-151">Для gRPC требуется HTTP/2, а HTTP/2 не поддерживает проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="db93d-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="db93d-152">Авторизация пользователей для доступа к службам и методам служб</span><span class="sxs-lookup"><span data-stu-id="db93d-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="db93d-153">По умолчанию все методы в службе могут вызываться пользователями, не прошедшими проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="db93d-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="db93d-154">Чтобы требовать проверку подлинности, примените к службе атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span><span class="sxs-lookup"><span data-stu-id="db93d-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="db93d-155">Можно использовать аргументы конструктора и свойства атрибута `[Authorize]`, чтобы ограничить доступ только пользователями, соответствующими определенным [политикам авторизации](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="db93d-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="db93d-156">Например, если имеется пользовательская политика авторизации с именем `MyAuthorizationPolicy`, используйте следующий код, чтобы доступ к службе могли получить только пользователи, соответствующие этой политике:</span><span class="sxs-lookup"><span data-stu-id="db93d-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="db93d-157">К отдельным методам службы также может применяться атрибут `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="db93d-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="db93d-158">Если текущий пользователь не соответствует политикам, применяемым к методу **и** классу, вызывающему объекту будет возвращена ошибка:</span><span class="sxs-lookup"><span data-stu-id="db93d-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="db93d-159">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="db93d-159">Additional resources</span></span>

* [<span data-ttu-id="db93d-160">Проверка подлинности с помощью маркера носителя в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db93d-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="db93d-161">Настройка проверки подлинности по сертификату клиента в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db93d-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
