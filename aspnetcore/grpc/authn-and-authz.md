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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Проверка подлинности и авторизация в gRPC для ASP.NET Core

Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Проверка подлинности пользователей, вызывающих службу gRPC

gRPC можно использовать с [проверкой подлинности ASP.NET Core](xref:security/authentication/identity), чтобы связать пользователя с каждым вызовом.

Ниже приведен пример `Startup.Configure`, в котором используется проверка подлинности gRPC и ASP.NET Core:

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
> Порядок, в котором регистрируется ПО промежуточного слоя ASP.NET Core для проверки подлинности, имеет значение. Всегда вызывайте `UseAuthentication` и `UseAuthorization` после `UseRouting` и до `UseEndpoints`.

Механизм проверки подлинности, используемый приложением во время вызова, необходимо настроить. Конфигурация проверки подлинности добавляется в `Startup.ConfigureServices` и будет отличаться в зависимости от используемого в приложении механизма проверки подлинности. Примеры защиты приложений ASP.NET Core см. в разделе [Примеры проверки подлинности](xref:security/authentication/samples).

После настройки проверки подлинности методы службы gRPC могут получить доступ к пользователю через `ServerCallContext`.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Проверка подлинности маркера носителя

Клиент может предоставить маркер доступа для проверки подлинности. Сервер проверяет маркер и использует его для обнаружения пользователя.

На сервере проверка подлинности маркера носителя настраивается с помощью [По промежуточного слоя JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

В клиенте .NET gRPC маркер можно отправить с помощью вызовов с использованием коллекции `Metadata`. Записи в коллекции `Metadata` отправляются с помощью вызова gRPC в виде заголовков HTTP:

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

Настройка `ChannelCredentials` в канале — это альтернативный способ отправки маркера службе с вызовами gRPC. Класс `ChannelCredentials` может содержать класс `CallCredentials`, что позволяет автоматически задавать `Metadata`.

Класс `CallCredentials` запускается каждый раз при вызове gRPC, что позволяет избежать необходимости писать код в нескольких местах для самостоятельной передачи маркера. Обратите внимание, что класс `CallCredentials` применяется только в том случае, если канал защищен с помощью TLS. Класс `CallCredentials` не применяется к незащищенным каналам без поддержки TLS.

Учетные данные в следующем примере настраивают канал для отправки маркера при каждом вызове gRPC:

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

### <a name="client-certificate-authentication"></a>Аутентификация на основе сертификата клиента

Клиент может также предоставить сертификат клиента для проверки подлинности. [Проверка подлинности по сертификату](https://tools.ietf.org/html/rfc5246#section-7.4.4) происходит на уровне TLS, задолго до его попадания в ASP.NET Core. Когда запрос попадает в ASP.NET Core, [пакет проверки подлинности сертификата клиента](xref:security/authentication/certauth) позволяет разрешить сертификат в `ClaimsPrincipal`.

> [!NOTE]
> Сервер должен быть настроен для принятия сертификатов клиентов. Сведения о принятии сертификатов клиента в Kestrel, IIS и Azure см. в статье <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.

В клиенте .NET gRPC сертификат клиента добавляется в `HttpClientHandler`, который затем используется для создания клиента gRPC:

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

### <a name="other-authentication-mechanisms"></a>Другие механизмы проверки подлинности

Многие механизмы проверки подлинности вASP.NET Core работают с gRPC:

* Azure Active Directory
* Сертификат клиента
* IdentityСервер
* Маркер JWT
* OAuth 2.0
* OpenID Connect
* WS-Federation

Дополнительные сведения о настройке проверки подлинности на сервере см. в разделе [Проверка подлинности в ASP.NET Core](xref:security/authentication/identity).

Настройка клиента gRPC для использования проверки подлинности будет зависеть от используемого механизма проверки подлинности. В предыдущих примерах маркера носителя и сертификата клиента показано несколько способов настройки клиента gRPC для отправки метаданных проверки подлинности с помощью вызовов gRPC:

* Строго типизированные клиенты gRPC используют `HttpClient` внутренне. Проверку подлинности можно настроить с помощью [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) или путем добавления пользовательских экземпляров [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) в `HttpClient`.
* Каждый вызов gRPC имеет необязательный аргумент `CallOptions`. Пользовательские заголовки можно отправлять с помощью коллекции заголовков параметра.

> [!NOTE]
> Проверка подлинности Windows (NTLM/Kerberos/Negotiate) не может использоваться с gRPC. Для gRPC требуется HTTP/2, а HTTP/2 не поддерживает проверку подлинности Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Авторизация пользователей для доступа к службам и методам служб

По умолчанию все методы в службе могут вызываться пользователями, не прошедшими проверку подлинности. Чтобы требовать проверку подлинности, примените к службе атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Можно использовать аргументы конструктора и свойства атрибута `[Authorize]`, чтобы ограничить доступ только пользователями, соответствующими определенным [политикам авторизации](xref:security/authorization/policies). Например, если имеется пользовательская политика авторизации с именем `MyAuthorizationPolicy`, используйте следующий код, чтобы доступ к службе могли получить только пользователи, соответствующие этой политике:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

К отдельным методам службы также может применяться атрибут `[Authorize]`. Если текущий пользователь не соответствует политикам, применяемым к методу **и** классу, вызывающему объекту будет возвращена ошибка:

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

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Проверка подлинности с помощью маркера носителя в ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Настройка проверки подлинности по сертификату клиента в ASP.NET Core](xref:security/authentication/certauth)
