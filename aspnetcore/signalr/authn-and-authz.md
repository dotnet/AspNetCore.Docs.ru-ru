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
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Проверка подлинности и авторизация в ASP.NET Core SignalR

[Эндрю Стантон-медперсонала](https://twitter.com/anurse)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Проверка подлинности пользователей, подключающихся к SignalR концентратору

SignalR можно использовать с [проверкой подлинности ASP.NET Core](xref:security/authentication/identity) , чтобы связать пользователя с каждым соединением. В центре данные проверки подлинности можно получить из свойства [хубконнектионконтекст. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . Проверка подлинности позволяет концентратору вызывать методы для всех соединений, связанных с пользователем. Дополнительные сведения см. [в разделе Управление пользователями и группами SignalR в ](xref:signalr/groups). Несколько подключений могут быть связаны с одним пользователем.

Ниже приведен пример `Startup.Configure` использования SignalR и ASP.NET Core проверки подлинности.

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
> Порядок, в котором регистрируется SignalR и ASP.NET Core по промежуточного слоя проверки подлинности имеет значение. Всегда вызывайте метод `UseAuthentication` перед `UseSignalR` , чтобы SignalR иметь пользователя в `HttpContext` .

::: moniker-end

### <a name="cookie-authentication"></a>Cookie аутентификация

В приложении, основанном на браузере, cookie Проверка подлинности позволяет существующим учетным данным пользователя автоматически передаваться на SignalR подключения. При использовании клиента браузера дополнительная настройка не требуется. Если пользователь вошел в приложение, SignalR подключение автоматически наследует эту проверку подлинности.

Cookies — это способ отправки маркеров доступа в браузере, но клиенты, не являющиеся браузерами, могут их отправить. При использовании [клиента .NET](xref:signalr/dotnet-client) `Cookies` свойство можно настроить в `.WithUrl` вызове, чтобы предоставить cookie . Однако для использования cookie проверки подлинности от клиента .NET приложение должно предоставить API для обмена данными проверки подлинности для cookie .

### <a name="bearer-token-authentication"></a>Проверка подлинности маркера носителя

Клиент может предоставить маркер доступа вместо использования cookie . Сервер проверяет маркер и использует его для обнаружения пользователя. Эта проверка выполняется только при установленном соединении. Во время жизни соединения сервер не выполняет автоматическую повторную проверку, чтобы проверить отзыв маркера.

В клиенте JavaScript маркер можно указать с помощью параметра [акцесстокенфактори](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

В клиенте .NET существует аналогичное свойство [акцесстокенпровидер](xref:signalr/configuration#configure-bearer-authentication) , которое можно использовать для настройки маркера:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Предоставляемая функция токена доступа вызывается перед **каждым** HTTP-запросом, выполненным SignalR . Если необходимо обновить токен, чтобы подключение было активно (так как оно может истечь во время подключения), сделайте это в этой функции и возвратите обновленный маркер.

В стандартных веб-API токены носителя отправляются в заголовке HTTP. Однако SignalR не может задать эти заголовки в браузерах при использовании некоторых транспортов. При использовании WebSockets и Server-Sent событий маркер передается как параметр строки запроса. 

#### <a name="built-in-jwt-authentication"></a>Встроенная проверка подлинности JWT

На сервере проверка подлинности токена носителя настраивается с помощью по [промежуточного слоя JWT Bearer](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> Строка запроса используется в браузерах при подключении с помощью WebSockets и событий Server-Sent из-за ограничений API браузера. При использовании HTTPS значения строки запроса защищаются с помощью подключения TLS. Однако многие серверы заносить в журнал значения строки запроса. Дополнительные сведения см. [в разделе вопросы безопасности в SignalR ASP.NET Core ](xref:signalr/security). SignalR использует заголовки для передачи токенов в средах, которые их поддерживают (например, клиенты .NET и Java).

#### <a name="identity-server-jwt-authentication"></a>Identity Проверка подлинности сервера JWT

При использовании Identity сервера добавьте <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> службу в проект:

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

Зарегистрируйте службу в `Startup.ConfigureServices` после добавления служб для проверки подлинности ( <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ) и обработчика проверки подлинности для Identity сервера ( <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ):

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="cookies-vs-bearer-tokens"></a>Cookieи токены носителя 

Cookieотносятся только к браузерам. Их отправка из других типов клиентов повышает сложность по сравнению с отправкой токенов носителя. Следовательно, cookie Проверка подлинности не рекомендуется, если приложению требуется только проверка подлинности пользователей только от клиента браузера. Проверка подлинности маркера носителя является рекомендуемым подходом при использовании клиентов, отличных от клиента браузера.

### <a name="windows-authentication"></a>Проверка подлинности Windows

Если в приложении настроена [Проверка подлинности Windows](xref:security/authentication/windowsauth) , SignalR может использовать это удостоверение для защиты концентраторов. Однако для отправки сообщений отдельным пользователям необходимо добавить настраиваемого поставщика ИДЕНТИФИКАТОРов пользователей. Система проверки подлинности Windows не предоставляет утверждение "идентификатор имени". SignalR использует утверждение для определения имени пользователя.

Добавьте новый класс, который реализует `IUserIdProvider` и извлекает одно из утверждений пользователя для использования в качестве идентификатора. Например, чтобы использовать утверждение "Name" (имя пользователя Windows в форме `[Domain]\[Username]` ), создайте следующий класс:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Вместо `ClaimTypes.Name` можно использовать любое значение из `User` (например, идентификатор SID Windows и т. д.).

> [!NOTE]
> Выбранное значение должно быть уникальным для всех пользователей в системе. В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.

Зарегистрируйте этот компонент в `Startup.ConfigureServices` методе.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

В клиенте .NET проверка подлинности Windows должна быть включена путем установки свойства [уседефаулткредентиалс](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Проверка подлинности Windows поддерживается в Internet Explorer и Microsoft погранично, но не во всех браузерах. Например, в Chrome и Safari попытка использовать проверку подлинности Windows и WebSockets завершается ошибкой. При сбое проверки подлинности Windows клиент пытается вернуться к другим транспортам, которые могут работать.

### <a name="use-claims-to-customize-identity-handling"></a>Использование утверждений для настройки обработки удостоверений

Приложение, выполняющее проверку подлинности пользователей, может наследовать SignalR идентификаторы пользователей от утверждений пользователей. Чтобы указать SignalR , как создаются идентификаторы пользователей, реализуйте `IUserIdProvider` и зарегистрируйте реализацию.

В примере кода показано использование утверждений для выбора адреса электронной почты пользователя в качестве идентифицирующего свойства. 

> [!NOTE]
> Выбранное значение должно быть уникальным для всех пользователей в системе. В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

При регистрации учетной записи в `ClaimsTypes.Email` базу данных удостоверений ASP.NET добавляется утверждение с типом.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Зарегистрируйте этот компонент в `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Авторизация пользователей для доступа к концентраторам и методам концентратора

По умолчанию все методы в концентраторе могут вызываться пользователем, не прошедшим проверку подлинности. Чтобы требовать проверку подлинности, примените к концентратору атрибут [авторизации](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) :

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Можно использовать аргументы конструктора и свойства атрибута `[Authorize]`, чтобы ограничить доступ только пользователями, соответствующими определенным [политикам авторизации](xref:security/authorization/policies). Например, при наличии пользовательской политики авторизации `MyAuthorizationPolicy` можно убедиться, что только пользователи, соответствующие этой политике, могут получить доступ к концентратору с помощью следующего кода:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Отдельные методы концентратора могут `[Authorize]` также применять атрибут. Если текущий пользователь не соответствует политике, примененной к методу, вызывающему объекту будет возвращена ошибка.

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Использование обработчиков авторизации для настройки авторизации метода концентратора

SignalR предоставляет пользовательский ресурс для обработчиков авторизации, когда методу концентратора требуется авторизация. Ресурс является экземпляром `HubInvocationContext`. `HubInvocationContext`Включает `HubCallerContext` , имя вызываемого метода концентратора и аргументы для метода концентратора.

Рассмотрим пример комнаты чатов, позволяющей нескольким организациям входить в систему через Azure Active Directory. Любой пользователь, у которого есть учетная запись Майкрософт, может войти в чат, но только члены владеющей Организации должны иметь возможность запрещать пользователям или просматривать историю разговора пользователей. Кроме того, может потребоваться ограничить некоторые функции от определенных пользователей. Использование обновленных функций в ASP.NET Core 3,0 — это вполне возможно. Обратите внимание, что `DomainRestrictedRequirement` служит в качестве пользовательского `IAuthorizationRequirement` . Теперь, когда `HubInvocationContext` передается параметр ресурса, внутренняя логика может проверить контекст, в котором вызывается концентратор, и принимать решения о том, чтобы разрешить пользователю выполнять отдельные методы концентратора.

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

В `Startup.ConfigureServices` добавьте новую политику, предоставив настраиваемое требование в `DomainRestrictedRequirement` качестве параметра для создания `DomainRestricted` политики.

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

В предыдущем примере `DomainRestrictedRequirement` класс является `IAuthorizationRequirement` и, и его собственным `AuthorizationHandler` для этого требования. Можно разделить эти два компонента на отдельные классы для разделения проблем. Преимуществом этого примера является отсутствие необходимости внедрять `AuthorizationHandler` во время запуска, так как требование и обработчик одинаковы.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Проверка подлинности токена носителя в ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Авторизация на основе ресурсов](xref:security/authorization/resourcebased)
