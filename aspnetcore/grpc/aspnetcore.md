---
title: Службы gRPC в ASP.NET Core
author: juntaoluo
description: Ознакомьтесь с основными понятиями при написании служб gRPC с помощью ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: 1a5510364ee46165e275d07073ab087d79d65313
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819046"
---
# <a name="grpc-services-with-aspnet-core"></a>Службы gRPC в ASP.NET Core

В этом документе показано, как приступить к работе со службами gRPC с помощью ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Начало работы со службой gRPC в ASP.NET Core

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Подробные инструкции по созданию проекта gRPC см. в статье [Начало работы со службами gRPC](xref:tutorials/grpc/grpc-start).

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Visual Studio для Mac](#tab/visual-studio-code+visual-studio-mac)

Из командной строки выполните команду `dotnet new grpc -o GrpcGreeter`.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Добавление служб gRPC в приложение ASP.NET Core

Для gRPC требуется пакет [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).

### <a name="configure-grpc"></a>Настройка gRPC

В файле *Startup.cs*:

* gRPC включается с помощью метода `AddGrpc`.
* Каждая служба gRPC добавляется в конвейер маршрутизации с помощью метода `MapGrpcService`.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ПО промежуточного слоя ASP.NET Core и компоненты совместно используют конвейер маршрутов, поэтому приложение можно настроить для обслуживания дополнительных обработчиков запросов. Дополнительные обработчики запросов, такие как контроллеры MVC, работают параллельно с настроенными службами gRPC.

## <a name="server-options"></a>Параметры сервера

Службы gRPC могут размещаться всеми встроенными серверами ASP.NET Core.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;Для работы служб IIS требуется .NET 5 и Windows 10 сборки 20241 или более поздней.

&Dagger;Для работы HTTP.sys требуется .NET 5 и Windows 10 сборки 19529 или более поздней.

Дополнительные сведения о выборе правильного сервера для приложения ASP.NET Core см. в статье <xref:fundamentals/servers/index>.

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) — это кроссплатформенный веб-сервер для ASP.NET Core. Kestrel обеспечивает максимальную производительность и эффективное использование памяти, однако в ней нет некоторых дополнительных функций в HTTP.sys, например общего доступа к портам.

Конечные точки gRPC Kestrel:

* Требуется HTTP/2.
* Безопасность следует обеспечивать с помощью [протокола TLS](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

Для gRPC требуется HTTP/2. gRPC для ASP.NET Core проверяет, что [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) имеет значение `HTTP/2`.

Kestrel [поддерживает HTTP/2](xref:fundamentals/servers/kestrel/http2) в большинстве современных операционных систем. Конечные точки Kestrel настроены для поддержки подключений HTTP/1.1 и HTTP/2 по умолчанию.

### <a name="tls"></a>TLS

Конечные точки Kestrel, используемые для gRPC, должны быть защищены с помощью TLS. При разработке конечная точка, защищенная с помощью TLS, автоматически создается в `https://localhost:5001` при наличии сертификата разработки ASP.NET Core. Настройка не требуется. Префикс `https` проверяет, что конечная точка Kestrel использует TLS.

В рабочей среде необходимо явно настроить TLS. В следующем примере *appsettings.json* предоставляется конечная точка HTTP/2, защищенная с помощью TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Кроме того, конечные точки Kestrel можно настроить в *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Согласование протокола

Протокол TLS используется не только для защиты обмена данными. Подтверждение установки связи TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) используется для согласования протокола подключения между клиентом и сервером, если конечная точка поддерживает несколько протоколов. Это согласование определяет, использует ли соединение HTTP/1.1 или HTTP/2.

Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`. Конечная точка с несколькими протоколами (например, `HttpProtocols.Http1AndHttp2`) не может использоваться без TLS, так как отсутствует согласование. Все подключения к незащищенной конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.

Дополнительные сведения о включении HTTP/2 и TLS с помощью Kestrel см. в разделе [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel/endpoints).

> [!NOTE]
> macOS не поддерживает ASP.NET Core gRPC с TLS. Для успешного запуска служб gRPC в macOS требуется дополнительная настройка. Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="iis"></a>IIS

[Службы IIS](xref:host-and-deploy/iis/index) — это гибкий, безопасный и управляемый веб-сервер для размещения веб-приложений, включая ASP.NET Core. Для размещения служб gRPC со службами IIS требуются .NET 5 и Windows 10 сборки 20241 или более поздней.

Службы IIS должны быть настроены для использования TLS и HTTP/2. Для получения дополнительной информации см. <xref:host-and-deploy/iis/protocols>.

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) — это веб-сервер для ASP.NET Core, который запускается только в Windows. Для размещения служб gRPC с HTTP.sys. требуются .NET 5 и Windows 10 сборки 19529 или более поздней.

HTTP.sys необходимо настроить для использования TLS и HTTP/2. Дополнительные сведения см. в разделе [Поддержка HTTP/2 для веб-сервера HTTP.sys](xref:fundamentals/servers/httpsys#http2-support).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) — это кроссплатформенный веб-сервер для ASP.NET Core. Kestrel обеспечивает максимальную производительность и эффективное использование памяти, однако в ней нет некоторых дополнительных функций в HTTP.sys, например общего доступа к портам.

Конечные точки gRPC Kestrel:

* Требуется HTTP/2.
* Безопасность следует обеспечивать с помощью [протокола TLS](https://tools.ietf.org/html/rfc5246).

### <a name="http2"></a>HTTP/2

Для gRPC требуется HTTP/2. gRPC для ASP.NET Core проверяет, что [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) имеет значение `HTTP/2`.

Kestrel [поддерживает HTTP/2](xref:fundamentals/servers/kestrel#http2-support) в большинстве современных операционных систем. Конечные точки Kestrel настроены для поддержки подключений HTTP/1.1 и HTTP/2 по умолчанию.

### <a name="tls"></a>TLS

Конечные точки Kestrel, используемые для gRPC, должны быть защищены с помощью TLS. При разработке конечная точка, защищенная с помощью TLS, автоматически создается в `https://localhost:5001` при наличии сертификата разработки ASP.NET Core. Настройка не требуется. Префикс `https` проверяет, что конечная точка Kestrel использует TLS.

В рабочей среде необходимо явно настроить TLS. В следующем примере *appsettings.json* предоставляется конечная точка HTTP/2, защищенная с помощью TLS:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Кроме того, конечные точки Kestrel можно настроить в *Program.cs*:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Согласование протокола

Протокол TLS используется не только для защиты обмена данными. Подтверждение установки связи TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) используется для согласования протокола подключения между клиентом и сервером, если конечная точка поддерживает несколько протоколов. Это согласование определяет, использует ли соединение HTTP/1.1 или HTTP/2.

Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`. Конечная точка с несколькими протоколами (например, `HttpProtocols.Http1AndHttp2`) не может использоваться без TLS, так как отсутствует согласование. Все подключения к незащищенной конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.

Дополнительные сведения о включении HTTP/2 и TLS с помощью Kestrel см. в разделе [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS не поддерживает ASP.NET Core gRPC с TLS. Для успешного запуска служб gRPC в macOS требуется дополнительная настройка. Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>Интеграция с API ASP.NET Core

Службы gRPC имеют полный доступ к компонентам ASP.NET Core, таким как [внедрение зависимостей](xref:fundamentals/dependency-injection) (DI) и [ведение журнала](xref:fundamentals/logging/index). Например, реализация службы может разрешить службу ведения журнала из контейнера DI с помощью конструктора:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

По умолчанию реализация службы gRPC может разрешать другие службы DI с любым временем существования (отдельное, ограниченное, временное).

### <a name="resolve-httpcontext-in-grpc-methods"></a>Разрешение HttpContext в методах gRPC

API gRPC предоставляет доступ к некоторым данным сообщений HTTP/2, таким как метод, узел, заголовок и трейлеры. Доступ осуществляется через аргумент `ServerCallContext`, передаваемый в каждый метод gRPC:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` не предоставляет полный доступ к `HttpContext` во всех API ASP.NET. Метод расширения `GetHttpContext` предоставляет полный доступ к `HttpContext`, представляющему базовое сообщение HTTP/2 в API ASP.NET:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
