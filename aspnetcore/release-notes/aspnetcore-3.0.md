---
title: Новые возможности в ASP.NET Core 3.0
author: rick-anderson
description: Сведения о новых возможностях в ASP.NET Core 3.0.
ms.author: riande
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
uid: aspnetcore-3.0
ms.openlocfilehash: 161d6aeefe1882b86ce97cdcfa7e5b23048693f0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217509"
---
# <a name="whats-new-in-aspnet-core-30"></a>Новые возможности в ASP.NET Core 3.0

В этой статье описываются наиболее важные изменения в ASP.NET Core 3.0 со ссылками на соответствующую документацию.

## Blazor

Blazor — это новая платформа в ASP.NET Core, предназначенная для создания интерактивного веб-интерфейса на стороне клиента с использованием .NET. Она воплощает следующие возможности:

* создание многофункциональных интерактивных пользовательских интерфейсов на C# вместо JavaScript;
* совместное использование серверной и клиентской логик приложений, написанных с помощью .NET;
* отображение пользовательского интерфейса в виде HTML-страницы с CSS для широкой поддержки браузеров, в том числе для мобильных устройств.

Поддерживаемые сценарии платформы Blazor:

* повторно используемые компоненты пользовательского интерфейса (компоненты Razor);
* маршрутизация на стороне клиента;
* макеты компонентов;
* поддержка внедрения зависимостей;
* Формы и проверка
* создание библиотек компонентов с помощью библиотек классов Razor;
* Взаимодействие с JavaScript

Для получения дополнительной информации см. <xref:blazor/index>.

### Blazor Server

Blazor отделяет логику отображения компонентов от того, как применяются обновления пользовательского интерфейса. Blazor Server предоставляет поддержку размещения компонентов Razor на сервере в приложении ASP.NET Core. Обновления пользовательского интерфейса передаются через подключение SignalR. Blazor Server поддерживается только в ASP.NET Core 3.0.

### <a name="blazor-webassembly-preview"></a>Blazor WebAssembly (предварительная версия)

Приложения Blazor можно также запускать напрямую в браузере с использованием среды выполнения .NET на основе WebAssembly. Платформа Blazor WebAssembly доступна в режиме предварительной версии и *не* поддерживается в ASP.NET Core 3.0. Blazor WebAssembly будет поддерживаться в будущем выпуске ASP.NET Core.

### <a name="razor-components"></a>составные части компонента Razor.

Приложения Blazor создаются на основе компонентов. Компоненты — это автономные блоки пользовательского интерфейса, такие как страница, диалоговое окно или форма. Это обычные классы .NET, определяющие логику отрисовки пользовательского интерфейса и обработчики событий на стороне клиента. Многофункциональные интерактивные веб-приложения можно создавать без JavaScript.

Компоненты в Blazor обычно создаются с использованием синтаксиса Razor, естественного сочетания HTML и C#. Компоненты Razor похожи на Razor Pages и представления MVC тем, что они используют Razor. В отличие от страниц и представлений, которые созданы на базе модели "запрос и ответ", компоненты используются исключительно для обработки компоновки пользовательского интерфейса.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* это популярная высокопроизводительная платформа RPC (удаленный вызов процедур).
* Для разработки API используется подход, при котором сначала создается контракт.
* Использует современные технологии, такие как:

  * HTTP/2 для транспортировки;
  * буферы протоколов в качестве языка описания интерфейса;
  * формат двоичной сериализации.
* Предоставляет следующие возможности:

  * Проверка подлинности
  * двунаправленная потоковая передача и управление потоком;
  * отмена и время ожидания.

К функциям gRPC в ASP.NET Core 3.0 относятся:

* [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore) Платформа ASP.NET Core для размещения служб gRPC. gRPC в ASP.NET Core поддерживает интеграцию со стандартными возможностями ASP.NET Core, такими как ведение журнала, внедрение зависимостей, проверка подлинности и авторизация.
* [Grpc.Net.Client.](https://www.nuget.org/packages/Grpc.Net.Client) Клиент gRPC для .NET Core, созданный на основе знакомого клиента `HttpClient`.
* [Grpc.Net.ClientFactory.](https://www.nuget.org/packages/Grpc.Net.ClientFactory) Интеграция клиента gRPC с `HttpClientFactory`.

Для получения дополнительной информации см. <xref:grpc/index>.

## SignalR

Инструкции по миграции см. в разделе об [обновлении кода SignalR](xref:migration/22-to-30#signalr). Для сериализации и десериализации сообщений JSON SignalR теперь использует `System.Text.Json`. Инструкции по восстановлению сериализатора на основе `Newtonsoft.Json` см. в разделе [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) (Переключение на Newtonsoft.JSON).

В клиентах JavaScript и .NET для SignalR добавлена поддержка автоматического повторного подключения. По умолчанию клиент пытается немедленно заново подключиться и повторить попытку через 2, 10 и 30 секунд при необходимости. Если клиент успешно повторно подключается, он получает новый идентификатор подключения. Автоматическое повторное подключение необходимо явно выбирать:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Интервалы повторного подключения можно указать, передав массив длительности в миллисекундах:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Пользовательскую реализацию можно передать для полного управления интервалами повторного подключения.

Если повторное подключение не удается выполнить после последнего интервала повторного подключения:

* клиент считает, что подключение находится в автономном режиме;
* клиент прекращает попытки повторного подключения.

Во время повторных попыток подключения обновите пользовательский интерфейс приложения, чтобы уведомить пользователя о попытке повторного подключения.

Чтобы обеспечить возможность отправлять отзывы о пользовательском интерфейсе при прерывании подключения, в API клиента SignalR добавлены следующие обработчики событий:

* `onreconnecting`.  Дает разработчикам возможность отключить пользовательский интерфейс или сообщить пользователям о том, что приложение находится в автономном режиме.
* `onreconnected`. Предоставляет разработчикам возможность обновлять пользовательский интерфейс после повторного установления соединения.

Следующий код использует `onreconnecting` для обновления пользовательского интерфейса при попытке подключения:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Следующий код использует `onreconnected` для обновления пользовательского интерфейса при подключении:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3.0 и более поздних версий предоставляется пользовательский ресурс для обработчиков авторизации, когда методу концентратора требуется авторизация. Ресурс является экземпляром `HubInvocationContext`. `HubInvocationContext` включает:

* `HubCallerContext`
* Имя вызываемого метода концентратора.
* Аргументы для метода концентратора.

Рассмотрим следующий пример приложения чата, разрешающего вход нескольких организаций с помощью Azure Active Directory. Любой пользователь с учетной записью Майкрософт может войти в чат, но запрещать пользователям принимать участие в обсуждениях или просматривать журналы чата пользователей могут только члены владеющей организации. Приложение может ограничивать определенные функции для определенных пользователей.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

В приведенном выше коде `DomainRestrictedRequirement` служит пользовательским `IAuthorizationRequirement`. Так как параметр ресурса `HubInvocationContext` передается, внутренняя логика может выполнять следующее:

* проверять контекст, в котором вызывается концентратор;
* принимать решения о разрешении пользователю выполнять отдельные методы концентратора.

Отдельные методы концентратора можно пометить именем политики, которую проверяет код во время выполнения. Когда клиенты пытаются вызвать отдельные методы концентратора, обработчик `DomainRestrictedRequirement` запускается и управляет доступом к методам. В зависимости от того, как `DomainRestrictedRequirement` управляет доступом:

* все вошедшие в систему пользователи могут вызывать метод `SendMessage`;
* просматривать журналы пользователей могут только пользователи, выполнившие вход с помощью адреса электронной почты `@jabbr.net`;
* запретить пользователям участвовать в обсуждениях могут только члены `bob42@jabbr.net`.

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
```

Для создания политики `DomainRestricted` может потребоваться сделать следующее:

* добавить новую политику в *Startup.cs*;
* предоставить настраиваемое требование `DomainRestrictedRequirement` в качестве параметра;
* зарегистрировать `DomainRestricted` с помощью ПО промежуточного слоя авторизации.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

Концентраторы SignalR используют [маршрутизацию конечных точек](xref:fundamentals/routing). Подключение концентратора SignalR было ранее выполнено явным образом:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

В предыдущей версии разработчикам требовалось подключать контроллеры, страницы Razor и концентраторы в различных местах. В результате явного подключения возникает последовательность практически идентичных сегментов маршрутизации:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

Концентраторы SignalR 3.0 можно маршрутизировать через конечные точки. При такой маршрутизации, как правило, можно настроить всю маршрутизацию в `UseRouting`.

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

С SignalR ASP.NET Core 3.0 добавлены новые возможности.

потоковая передача между клиентом и сервером. При потоковой передаче между клиентом и сервером методы на стороне сервера могут принимать экземпляры `IAsyncEnumerable<T>` или `ChannelReader<T>`. В следующем примере C# метод `UploadStream` в концентраторе получит поток строк от клиента:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Клиентские приложения .NET могут передавать экземпляр `IAsyncEnumerable<T>` или `ChannelReader<T>` в качестве аргумента `stream` для метода концентратора `UploadStream`, приведенного выше.

После завершения цикла `for` и завершения работы локальной функции отправляется завершение потока:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Клиентские приложения JavaScript используют `Subject` SignalR (или [субъект RxJS](https://rxjs.dev/api/index/class/Subject)) для аргумента `stream` метода концентратора `UploadStream`, приведенного выше.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Код JavaScript может использовать метод `subject.next` для обработки строк по мере их записи и готовности к отправке на сервер.

```javascript
subject.next("example");
subject.complete();
```

С помощью кода, подобного двум предыдущим фрагментам, можно создать потоковую передачу в реальном времени.

## <a name="new-json-serialization"></a>Новая сериализация JSON

ASP.NET Core 3.0 теперь по умолчанию использует <xref:System.Text.Json> для сериализации JSON:

* асинхронно считывает и записывает JSON;
* оптимизирован для текста UTF-8;
* предоставляет более высокую производительность, чем `Newtonsoft.Json`.

Сведения о добавлении Json.NET в ASP.NET Core 3.0 см. в разделе [Добавление поддержки формата JSON на основе Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Новые директивы Razor

Следующий список содержит новые директивы Razor.

* [`@attribute`](xref:mvc/views/razor#attribute): Директива `@attribute` применяет этот атрибут к классу созданной страницы или представления. Например, `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements): Директива `@implements` реализует интерфейс для созданного класса. Например, `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 поддерживает проверку подлинности и авторизацию для веб-API и одностраничных приложений

ASP.NET Core 3.0 обеспечивает проверку подлинности в одностраничных приложениях с помощью поддержки авторизации веб-API. ASP.NET Core Identity для проверки подлинности и хранения данных пользователей объединяется с [IdentityServer4](https://identityserver.io/) для реализации OpenID Connect.

IdentityServer4 — это платформа OpenID Connect и OAuth 2.0 для ASP.NET Core 3.0. Она обеспечивает следующие функции безопасности:

* Проверка подлинности как услуга (AaaS)
* Единый вход (SSO) для нескольких типов приложений
* Контроль доступа для API
* Шлюз федерации

Дополнительные сведения см. в [документации по IdentityServer4](http://docs.identityserver.io/en/latest/index.html) или статье [Проверка подлинности и авторизация для одностраничных приложений](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Проверка подлинности Kerberos и проверка подлинности с помощью сертификата

Для проверки подлинности с помощью сертификата требуется:

* настройка сервера для принятия сертификатов;
* добавление ПО промежуточного слоя для проверки подлинности в `Startup.Configure`;
* добавление службы проверки подлинности с помощью сертификатов в `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Параметры проверки подлинности с помощью сертификата включают следующие возможности:

* принятие самозаверяющих сертификатов;
* проверка отзыва сертификатов;
* проверка наличия в предложенном сертификате правильных флагов использования.

Субъект-пользователь по умолчанию создается на основе свойств сертификата. Субъект-пользователь содержит событие, которое позволяет дополнить или заменить субъект. Для получения дополнительной информации см. <xref:security/authentication/certauth>.

[Проверка подлинности Windows](/windows-server/security/windows-authentication/windows-authentication-overview) теперь предусмотрена для Linux и macOS. В предыдущих версиях аутентификация Windows была доступна только для [IIS](xref:host-and-deploy/iis/index) и [HTTP.sys](xref:fundamentals/servers/httpsys). В ASP.NET Core 3.0 [Kestrel](xref:fundamentals/servers/kestrel) имеет возможность использовать Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) и [NTLM в Windows](/windows-server/security/kerberos/ntlm-overview), Linux и macOS для узлов, присоединенных к домену Windows. Поддержка Kestrel этих схем проверки подлинности предоставляется в пакете [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate). Как и в других службах проверки подлинности, настройте приложение проверки подлинности во всей организации, а затем настройте службу:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Требования к узлу:

* На узлах Windows [имена субъектов-служб](/windows/win32/ad/service-principal-names) должны быть добавлены в учетную запись пользователя, где размещается приложение.
* Компьютеры Linux и macOS должны быть присоединены к домену.
  * Имена субъектов-служб необходимо создать для веб-процесса.
  * [Файлы Keytab](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) необходимо создать и настроить на компьютере узла.

Для получения дополнительной информации см. <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Изменения шаблонов

В шаблонах веб-интерфейса (Razor Pages, MVC с контроллером и представлениями) удалены следующие элементы.

* Пользовательского интерфейса согласия на файлы cookie больше нет. Сведения о включении функции согласия на файлы cookie в приложении, созданном с помощью шаблона ASP.NET Core 3.0, см. в статье <xref:security/gdpr>.
* Для ссылки на скрипты и связанные статические ресурсы теперь используются локальные файлы, а не CDN. Дополнительные сведения см. в статье [3.0: Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment](https://github.com/dotnet/AspNetCore.Docs/issues/14350) (В версии 3.0 для ссылки на скрипты и связанные статические ресурсы теперь используются локальные файлы, а не CDN в зависимости от текущей среды) (№ 14350).

Шаблон Angular обновлен для использования Angular 8.

По умолчанию для шаблона библиотеки классов Razor (RCL) используется разработка компонентов Razor. Новый параметр шаблона в Visual Studio обеспечивает поддержку шаблонов для страниц и представлений. При создании RCL на основе шаблона в командной оболочке передайте параметр `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).

## <a name="generic-host"></a>Универсальный узел

Шаблоны ASP.NET Core 3.0 используют <xref:fundamentals/host/generic-host>. В предыдущих версиях использовался <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>. Использование универсального узла .NET Core (<xref:Microsoft.Extensions.Hosting.HostBuilder>) обеспечивает лучшую интеграцию приложений ASP.NET Core с другими серверными сценариями, не зависящими от Интернета. Дополнительные сведения см. в разделе [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder) (HostBuilder заменяет WebHostBuilder).

### <a name="host-configuration"></a>Конфигурация узла

До выхода ASP.NET Core 3.0 переменные среды с префиксом `ASPNETCORE_` были загружены для конфигурации веб-узла. В 3.0 `AddEnvironmentVariables` используется для загрузки переменных среды с префиксом `DOTNET_` для конфигурации узла с помощью `CreateDefaultBuilder`.

### <a name="changes-to-startup-constructor-injection"></a>Изменения во внедрении через конструктор Startup

Универсальный узел поддерживает только следующие типы для внедрения через конструктор `Startup`:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Все службы по-прежнему можно непосредственно внедрять в метод `Startup.Configure` в качестве аргументов. Дополнительные сведения см. в статье [Generic Host restricts Startup constructor injection](https://github.com/aspnet/Announcements/issues/353) (Универсальный узел ограничивает внедрение через конструктор Startup) (№ 353).

## <a name="kestrel"></a>Kestrel

* В конфигурацию Kestrel добавлена возможность миграции в универсальный узел. В 3.0 Kestrel настраивается в построителе веб-узлов, предоставляемом `ConfigureWebHostDefaults`.
* Адаптеры подключений удалены из Kestrel и заменены на ПО промежуточного слоя подключения, которое похоже на ПО промежуточного слоя HTTP в конвейере ASP.NET Core, но для подключений более низкого уровня.
* Транспортный уровень Kestrel предоставляется как открытый интерфейс в `Connections.Abstractions`.
* Неоднозначность заголовков и конечных строк устранена путем перемещения конечных заголовков в новую коллекцию.
* Из-за таких интерфейсов API с синхронными операциями ввода-вывода, как `HttpRequest.Body.Read`, часто возникает нехватка потоков, что приводит к сбоям приложений. В 3.0 `AllowSynchronousIO` отключен по умолчанию.

Для получения дополнительной информации см. <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 включено по умолчанию.

В Kestrel для конечных точек HTTPS по умолчанию включено HTTP/2. Поддержка HTTP/2 для IIS или HTTP.sys включена, если поддерживается операционной системой.

## <a name="eventcounters-on-request"></a>EventCounters по запросу

EventSource размещения, `Microsoft.AspNetCore.Hosting`, выдает следующие новые типы <xref:System.Diagnostics.Tracing.EventCounter>, связанные с входящими запросами:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Маршрутизация конечных точек

Маршрутизация конечных точек, позволяющая платформам (например, MVC) хорошо работать с ПО промежуточного слоя, усовершенствована:

* порядок ПО промежуточного слоя и конечных точек можно настроить в конвейере обработки запросов `Startup.Configure`;
* конечные точки и ПО промежуточного слоя хорошо используются с другими технологиями на основе ASP.NET Core, такими как проверки работоспособности;
* конечные точки могут реализовывать политику, например CORS или авторизацию, в ПО промежуточного слоя и MVC;
* фильтры и атрибуты можно разместить в методах контроллеров.

Для получения дополнительной информации см. <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Проверки работоспособности

Для проверок работоспособности используется маршрутизация конечных точек с универсальным узлом. В `Startup.Configure` вызовите `MapHealthChecks` для построителя конечной точки с URL-адресом конечной точки или относительным путем:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Конечные точки проверки работоспособности могут:

* указать один или несколько разрешенных узлов или портов;
* требовать авторизацию;
* требовать CORS.

Дополнительные сведения см. в следующих статьях:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Каналы в HttpContext

Теперь можно читать текст запроса и писать текст ответа с помощью API <xref:System.IO.Pipelines>. Классу <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> Свойство `HttpRequest.BodyReader` предоставляет класс <xref:System.IO.Pipelines.PipeReader>, который можно использовать для чтения текста запроса. Классу <!-- <xref:Microsoft.AspNetCore.Http.> --> Свойство `HttpResponse.BodyWriter` предоставляет класс <xref:System.IO.Pipelines.PipeWriter>, который можно использовать для записи текста ответа. `HttpRequest.BodyReader` — это аналог потока `HttpRequest.Body`. `HttpResponse.BodyWriter` — это аналог потока `HttpResponse.Body`.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Улучшены сообщения об ошибках в IIS

Теперь при ошибках запуска во время размещения приложений ASP.NET Core в IIS предоставляются более полные диагностические данные. Сведения об этих ошибках передаются в журнал событий Windows с трассировками стека везде, где это применимо. Кроме того, все предупреждения, ошибки и необработанные исключения записываются в журнал событий Windows.

## <a name="worker-service-and-worker-sdk"></a>Служба рабочих ролей и пакет SDK для рабочей роли

В .NET Core 3.0 появился новый шаблон приложения службы рабочих ролей. Этот шаблон может служить отправной точкой для написания длительных приложений служб в .NET Core.

Дополнительные сведения можно найти в разделе

* [.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/) (Рабочие роли .NET Core в качестве служб Windows)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Улучшения ПО промежуточного слоя перенаправления заголовков

В предыдущих версиях ASP.NET Core вызвать <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> и <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> было проблематично при развертывании в Azure Linux или за любым обратным прокси-сервером, отличным от IIS. Исправление для предыдущих версий описано в разделе [Переадресация схемы для Linux и обратных прокси-серверов не IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Этот сценарий исправлен в ASP.NET Core 3.0. Узел включает [ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options), если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`. В образах контейнера для `ASPNETCORE_FORWARDEDHEADERS_ENABLED` задано значение `true`.

## <a name="performance-improvements"></a>Улучшения производительности

В ASP.NET Core 3.0 реализованы многочисленные улучшения, сокращающие использование памяти и повышающие пропускную способность:

* сокращение использования памяти при использовании встроенного контейнера внедрения зависимостей для служб с заданной областью;
* сокращение количества распределений на платформе, включая сценарии ПО промежуточного слоя и маршрутизацию;
* сокращение использования памяти для подключений WebSocket;
* сокращение использования памяти и улучшение пропускной способности для подключений по протоколу HTTPS;
* новый оптимизированный и полностью асинхронный сериализатор JSON;
* сокращение использования памяти и улучшения пропускной способности при анализе формы.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 работает только в .NET Core 3.0

Начиная с ASP.NET Core 3.0, .NET Framework больше не является поддерживаемой целевой платформой. Проекты, предназначенные для .NET Framework, можно полноценно использовать с помощью [выпуска LTS .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1). Большинство пакетов, связанных с ASP.NET Core 2.1.x, будут поддерживаться неограниченно после истечения трехлетнего периода LTS для .NET Core 2.1.

Сведения о миграции см. в статье [Перенос кода в .NET Core из .NET Framework](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Использование общей платформы .NET Core

Для общей платформы ASP.NET Core 3.0, содержащейся в [метапакете Microsoft.AspNetCore.app](xref:fundamentals/metapackage-app), больше не требуется явный элемент `<PackageReference />` в файле проекта. При использовании пакета SDK `Microsoft.NET.Sdk.Web` в файле проекта автоматически создается ссылка на общую платформу:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Сборки, удаленные из общей платформы ASP.NET Core

Наиболее важные сборки, удаленные из общей платформы ASP.NET Core 3.0:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Сведения о добавлении Json.NET в ASP.NET Core 3.0 см. в разделе [Добавление поддержки формата JSON на основе Newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). В ASP.NET Core 3.0 внедрен `System.Text.Json` для чтения файла JSON и записи в него. Дополнительные сведения см. в разделе [Новая сериализация JSON](#new-json-serialization) в этом документе.
* [Entity Framework Core](/ef/core/)

Полный список сборок, удаленных из общей платформы, см. в разделе [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755) (Сборки, удаленные из Microsoft.AspNetCore.App 3.0). Дополнительные сведения о мотивации для этого изменения см. в статье [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) (Критические изменения в Microsoft.AspNetCore.App 3.0) и записи блога [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Первое знакомство с изменениями в ASP.NET Core 3.0).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
