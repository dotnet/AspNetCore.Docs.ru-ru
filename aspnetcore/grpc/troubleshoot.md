---
title: Устранение неполадок gRPC в .NET Core
author: jamesnk
description: Устранение ошибок при использовании gRPC в .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: 61d4d2204886f26b4ff55bc876825012809f1dfa
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253102"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Устранение неполадок gRPC в .NET Core

Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)

В этом документе рассматриваются часто возникающие проблемы при разработке приложений gRPC на платформе .NET.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Несоответствие конфигурации SSL/TLS клиента и службы

В шаблоне и образцах gRPC для защиты служб gRPC по умолчанию используется [протокол TLS](https://tools.ietf.org/html/rfc5246). Клиенты gRPC должны использовать безопасное соединение для успешного вызова защищенных служб gRPC.

Проверить, использует ли служба gRPC ASP.NET Core протокол TLS, можно в журналах, создаваемых при запуске приложения. Служба будет ожидать передачи данных через конечную точку HTTPS:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Для выполнения вызовов через защищенное соединение клиент .NET Core должен использовать `https` в адресе сервера:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Все реализации клиента gRPC поддерживают протокол TLS. Для клиентов gRPC на других языках обычно требуется канал, настроенный с помощью `SslCredentials`. В `SslCredentials` указывается сертификат, который будет использоваться клиентом вместо незащищенных учетных данных. Примеры настройки протокола TLS для различных реализаций клиента gRPC см. в статье, посвященной [проверке подлинности gRPC](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Вызов службы gRPC с использованием ненадежного или недействительного сертификата

Клиент gRPC .NET требует, чтобы у службы был надежный сертификат. При вызове службы gRPC без надежного сертификата возвращается следующее сообщение об ошибке:

> Необработанное исключение. System.Net.Http.HttpRequestException: не удалось установить подключение SSL. См. внутреннее исключение.
> ---> System.Security.Authentication.AuthenticationException: Удаленный сертификат недопустим согласно процедуре проверки.

Эта ошибка может возникать, если при локальном тестировании приложения сертификат разработки HTTPS ASP.NET Core не является надежным. См. сведения об устранении этой проблемы в руководстве по [настройке доверия к сертификату разработки HTTPS ASP.NET Core в Windows и macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Если вы вызываете службу gRPC на другом компьютере и не можете установить доверие к сертификату, то клиент gRPC можно настроить так, чтобы недействительный сертификат игнорировался. В следующем коде с помощью [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) разрешаются вызовы без надежного сертификата:

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Ненадежные сертификаты следует использовать только во время разработки приложения. В рабочих приложениях всегда должны использоваться действительные сертификаты.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Вызов незащищенных служб gRPC с помощью клиента .NET Core

Если приложение использует .NET Core 3.x, для вызова незащищенных служб gRPC с помощью клиента .NET Core требуется дополнительная настройка. Клиент gRPC должен установить параметр `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` в значение `true` и использовать `http` в адресе сервера:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

Приложениям .NET 5 не требуется дополнительная настройка, но для вызова незащищенных служб gRPC они должны использовать `Grpc.Net.Client` версии 2.32.0 или более поздней.

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>Не удается запустить приложение gRPC ASP.NET Core в macOS

Kestrel не поддерживает HTTP/2 с TLS в macOS и старых версиях Windows, таких как Windows 7. В шаблоне и образцах gRPC ASP.NET Core по умолчанию используется протокол TLS. При попытке запустить сервер gRPC появится следующее сообщение об ошибке:

> Не удалось выполнить привязку к https://localhost:5001 в интерфейсе замыкания на себя IPv4: "'HTTP/2 через TLS не поддерживается в macOS из-за отсутствия поддержки ALPN."

Для решения этой проблемы настройте Kestrel и клиент gRPC так, чтобы они использовали HTTP/2 *без* TLS. Это следует делать только во время разработки. Если протокол TLS не используется, сообщения gRPC передаются без шифрования.

Для Kestrel должна быть настроена конечная точка HTTP/2 без TLS в файле *Program.cs*:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker range=">= aspnetcore-5.0"
Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2` использовать нельзя, так как протокол TLS необходим для согласования подключения по HTTP/2. Без TLS все подключения к конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.
::: moniker-end

::: moniker range="< aspnetcore-5.0"
Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`. `HttpProtocols.Http1AndHttp2` использовать нельзя, так как протокол TLS необходим для согласования подключения по HTTP/2. Без TLS все подключения к конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.
::: moniker-end

Клиент gRPC также должен быть настроен так, чтобы протокол TLS не использовался. Дополнительные сведения см. в разделе [Вызов незащищенных служб gRPC с помощью клиента .NET Core](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> Протокол HTTP/2 без TLS следует использовать только во время разработки приложения. В рабочих приложениях всегда должна использоваться защита транспорта. Дополнительные сведения см. в статье [Вопросы безопасности в gRPC для ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>Код ресурсов gRPC на C# не создается из файлов PROTO

Для создания кода для конкретных клиентов и базовых классов службы gRPC необходимо, чтобы проект ссылался на файлы и инструментарий protobuf. Необходимо включить следующее:

* нужные файлы *PROTO* в группе элементов `<Protobuf>`; проект должен ссылаться на [импортированные файлы *PROTO*](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions);
* ссылку на пакет инструментария gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).

Дополнительные сведения о создании ресурсов gRPC на C# см. в статье <xref:grpc/basics>.

Веб-приложение ASP.NET Core, в котором размещаются службы gRPC, требует создания только базового класса службы:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Клиент gRPC, выполняющий вызовы gRPC, требует создания только конкретного клиента:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>В проектах WPF не удается создать ресурсы gRPC на C# из файлов PROTO

В проектах WPF есть [известная проблема](https://github.com/dotnet/wpf/issues/810), из-за которой создание кода gRPC работает неправильно. При использовании любых типов gRPC, созданных в проектах WPF посредством ссылки на `Grpc.Tools` и файлы *PROTO*, будут возникать ошибки компиляции.

> Ошибка CS0246: Не удалось найти тип или имя пространства имен "MyGrpcServices" (возможно, отсутствует директива using или ссылка на сборку).

Эту проблему можно решить, выполнив указанные ниже действия.

1. Создайте проект библиотеки классов .NET Core.
2. В новом проекте добавьте ссылки, чтобы включить [создание кода C# из файлов *\*PROTO*](xref:grpc/basics#generated-c-assets).
    * Добавьте ссылку на пакет [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).
    * Добавьте файлы *\*.proto* в группу элементов `<Protobuf>`.
3. В приложении WPF добавьте ссылку на новый проект.

Приложение WPF может использовать созданные типы gRPC из нового проекта библиотеки классов.

[!INCLUDE[](~/includes/gRPCazure.md)]
