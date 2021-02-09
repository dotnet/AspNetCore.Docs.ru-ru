---
title: Использование gRPC в приложениях на основе браузера
author: jamesnk
description: Узнайте, как настроить службы gRPC на платформе ASP.NET Core для вызова из приложений браузера с помощью gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 0967a70b498156d9c4ea8818ee1c80b37d9f2d87
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217483"
---
# <a name="use-grpc-in-browser-apps"></a>Использование gRPC в приложениях на основе браузера

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

 Узнайте, как настроить существующую службу ASP.NET Core gRPC для вызовов из приложений браузера с использованием протокола [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md). gRPC-Web позволяет приложениям JavaScript и Blazor на основе браузера вызывать службы gRPC. Вызвать службу HTTP/2 gRPC из приложения на основе браузера нельзя. Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC.


См. раздел [Добавление служб gRPC в приложение ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Инструкции по созданию проекта gRPC см. здесь: <xref:tutorials/grpc/grpc-start>.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web в ASP.NET Core или Envoy

Существует два способа добавления gRPC-Web в приложение ASP.NET Core.

* Поддержка gRPC-Web вместе с gRPC HTTP/2 в ASP.NET Core. Этот параметр использует ПО промежуточного слоя, предоставленное пакетом `Grpc.AspNetCore.Web`.
* Используйте поддержку gRPC-Web в [прокси-сервере Envoy](https://www.envoyproxy.io/), чтобы транслировать gRPC-Web в gRPC HTTP/2. Затем переведенный вызов перенаправляется в приложение ASP.NET Core.

Есть свои плюсы и минусы этого подхода. Если среда приложения уже использует Envoy в качестве прокси-сервера, имеет смысл использовать Envoy и для предоставления поддержки gRPC-Web. Если требуется простое решение для gRPC-Web, для которого требуется только ASP.NET Core, используйте `Grpc.AspNetCore.Web`.

## <a name="configure-grpc-web-in-aspnet-core"></a>Настройка gRPC-Web в ASP.NET Core

Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC. gRPC-Web не требует вносить изменения в службы. Единственного изменения потребует конфигурация запуска.

Чтобы включить gRPC-Web со службой gRPC ASP.NET Core, выполните следующие действия.

* Добавьте ссылку на пакет [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).
* Настройте приложение на использование gRPC-Web, добавив `UseGrpcWeb` и `EnableGrpcWeb` в файл *Startup.cs*:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Предыдущий код:

* Добавляет ПО промежуточного слоя gRPC-Web (`UseGrpcWeb`) после маршрутизации и перед конечными точками.
* Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой gRPC-Web с `EnableGrpcWeb`. 

Также можно настроить ПО промежуточного слоя gRPC-Web, чтобы все службы поддерживали gRPC-Web по умолчанию и не нужно было использовать `EnableGrpcWeb`. Укажите `new GrpcWebOptions { DefaultEnabled = true }` при добавлении ПО промежуточного слоя.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Существует известная ошибка, приводящая к сбою gRPC-Web при [размещении с помощью HTTP.sys](xref:fundamentals/servers/httpsys) в .NET Core 3.x.
>
> Временное решение для запуска gRPC-Web на HTTP. sys доступно [здесь](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web и CORS

Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение применяется к вызовам gRPC-Web с приложениями браузера. Например, приложение браузера, обслуживаемое `https://www.contoso.com`, блокирует вызов служб gRPC-Web, размещенных на `https://services.contoso.com`. Можно использовать общий доступ к ресурсам независимо от источника (CORS), чтобы ослабить это ограничение.

Чтобы разрешить приложению браузера вызывать gRPC-Web независимо от источника, настройте [CORS в ASP.NET Core](xref:security/cors). Используйте встроенную поддержку CORS и предоставьте заголовки, относящиеся к gRPC, с помощью <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Предыдущий код:

* Вызывает `AddCors` для добавления служб CORS и настраивает политику CORS, которая предоставляет заголовки, относящиеся к gRPC.
* Вызывает `UseCors` для добавления ПО промежуточного слоя CORS после маршрутизации и перед конечными точками.
* Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой CORS с `RequiresCors`.

### <a name="grpc-web-and-streaming"></a>gRPC-Web и потоковая передача

Традиционный API gRPC по HTTP/2 поддерживает потоковую передачу во всех направлениях. gRPC-Web имеет ограниченную поддержку потоковой передачи:

* Клиенты браузера с gRPC-Web не поддерживают вызов методов потоковой передачи клиента и двунаправленной потоковой передачи.
* Службы gRPC в ASP.NET Core, размещенные в Службе приложений Azure и IIS, не поддерживают двунаправленную потоковую передачу.

При использовании gRPC-Web мы рекомендуем применять только унарные методы и методы серверной потоковой передачи.

## <a name="call-grpc-web-from-the-browser"></a>Вызов gRPC-Web из браузера

Приложения браузера могут использовать gRPC-Web для вызова служб gRPC. При вызове служб gRPC с помощью gRPC-Web из браузера существует ряд требований и ограничений.

* Сервер должен быть настроен для поддержки gRPC-Web.
* Потоковая передача клиента и вызовы двунаправленной потоковой передачи не поддерживаются. Потоковая передача сервера поддерживается.
* Для вызова служб gRPC в другом домене требуется настроить [CORS](xref:security/cors) на сервере.

### <a name="javascript-grpc-web-client"></a>Клиент gRPC-Web JavaScript

Существует клиент gRPC-Web JavaScript. Инструкции по использованию gRPC-Web из JavaScript см. в статье, посвященной [написанию кода клиента JavaScript с gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Настройка gRPC-Web с помощью клиента .NET gRPC

Клиент .NET gRPC можно настроить для выполнения вызовов gRPC-Web. Это полезно для приложений [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), которые размещаются в браузере и имеют те же ограничения HTTP, что и код JavaScript. Вызов gRPC-Web с помощью клиента .NET выполняется [так же, как и HTTP/2 gRPC](xref:grpc/client). Единственным изменением является то, как создается канал.

Чтобы использовать gRPC-Web:

* Добавьте ссылку на пакет [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).
* Убедитесь, что используется ссылка на пакет [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.29.0 или более поздней.
* Настройте канал на использование `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Предыдущий код:

* Настраивает канал для использования gRPC-Web.
* Создает клиент и выполняет вызов с помощью канала.

`GrpcWebHandler` имеет следующие параметры конфигурации.

* **InnerHandler**: базовый <xref:System.Net.Http.HttpMessageHandler>, который выполняет HTTP-запрос gRPC, например `HttpClientHandler`.
* **GrpcWebMode**: Тип перечисления, указывающий, является ли HTTP-запрос gRPC `Content-Type` `application/grpc-web` или `application/grpc-web-text`.
    * `GrpcWebMode.GrpcWeb` настраивает содержимое для отправки без кодировки. Значение по умолчанию.
    * `GrpcWebMode.GrpcWebText` настраивает содержимое в кодировке Base64. Требуется для вызовов потоковой передачи сервера в браузерах.
* **HttpVersion**: `Version` протокола HTTP, используемая для задания [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) в базовом HTTP-запросе gRPC. gRPC-Web не требует определенной версии и не переопределяет значение по умолчанию, если не указано иное.

> [!IMPORTANT]
> Созданные клиенты gRPC имеют синхронные и асинхронные методы для вызова унарных методов. Например, `SayHello` является синхронным, а `SayHelloAsync` — асинхронным. Вызов синхронного метода в приложении Blazor WebAssembly приведет к тому, что приложение перестанет отвечать на запросы. В Blazor WebAssembly всегда следует использовать асинхронные методы.

### <a name="use-grpc-client-factory-with-grpc-web"></a>Использование фабрики клиента gRPC с gRPC-Web

Клиент .NET, совместимый с gRPC-Web, можно создать путем интеграции gRPC с [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).

Чтобы использовать gRPC-Web с фабрикой клиента, выполните следующие действия.

* Добавьте в файл проекта следующие ссылки на пакеты:
  * [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* Зарегистрируйте клиент gRPC с внедрением зависимостей (DI) с помощью универсального метода расширения `AddGrpcClient`. В приложении Blazor WebAssembly службы регистрируются с внедрением зависимостей в `Program.cs`.
* Настройте `GrpcWebHandler` с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A>.

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

Для получения дополнительной информации см. <xref:grpc/clientfactory>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Проект GitHub — gRPC для веб-клиентов](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
