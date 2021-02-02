---
title: Настройка конечных точек для веб-сервера Kestrel для ASP.NET Core
author: rick-anderson
description: Сведения о настройке конечных точек с использованием Kestrel, кроссплатформенного веб-сервера для ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/endpoints
ms.openlocfilehash: 5fec573013da5bcb5039b7a189fd84d964349b3a
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658746"
---
# <a name="configure-endpoints-for-the-aspnet-core-kestrel-web-server"></a>Настройка конечных точек для веб-сервера Kestrel для ASP.NET Core

По умолчанию платформа ASP.NET Core привязана к:

* `http://localhost:5000`
* `https://localhost:5001` (если присутствует локальный сертификат разработки)

Укажите URL-адреса с помощью следующих параметров:

* Переменная среды `ASPNETCORE_URLS`.
* Аргументы командной строки `--urls`.
* Ключ конфигурации узла `urls`.
* Метод расширения `UseUrls`.

Значение, указанное с помощью этих подходов, может быть одной или несколькими конечными точками HTTP и HTTPS (HTTPS при наличии сертификата по умолчанию). Настройте значение в виде списка с разделением точкой с запятой (например, `"Urls": "http://localhost:8000;http://localhost:8001"`).

Дополнительные сведения о таких подходах см. в разделах [URL-адреса сервера](xref:fundamentals/host/web-host#server-urls) и [Переопределение конфигурации](xref:fundamentals/host/web-host#override-configuration).

Сертификат разработки создается, когда:

* установлен [пакет SDK для .NET](/dotnet/core/sdk);
* используется [средство dev-certs](xref:aspnetcore-2.1#https) для создания сертификата.

В некоторых браузерах требуется явное разрешение доверять локальному сертификату разработки.

Шаблоны проектов настраивают приложения так, чтобы они запускались на базе HTTPS по умолчанию и включали [поддержку перенаправления HTTPS и HSTS](xref:security/enforcing-ssl).

Вызовите методы <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> или <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> из <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>, чтобы настроить префиксы URL-адресов и порты для Kestrel.

`UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` и переменная среды `ASPNETCORE_URLS` тоже работают, однако на них распространяются ограничения, указанные далее в этой статье (для конфигурации конечной точки HTTPS требуется сертификат по умолчанию).

Конфигурация `KestrelServerOptions`:

## <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults(Action\<ListenOptions>)

Указывает конфигурацию `Action` для выполнения каждой заданной конечной точки. Если вызвать `ConfigureEndpointDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults%2A>, не будут применяться значения по умолчанию.

## <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)

Указывает конфигурацию `Action` для выполнения каждой конечной точки HTTPS. Если вызвать `ConfigureHttpsDefaults` несколько раз, предыдущие элементы `Action` будут заменены последним элементом `Action`.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults%2A>, не будут применяться значения по умолчанию.

## <a name="configureiconfiguration"></a>Configure(IConfiguration)

Создает загрузчик конфигурации для настройки Kestrel, который принимает <xref:Microsoft.Extensions.Configuration.IConfiguration> в качестве входных данных. Для Kestrel конфигурация должна быть ограничена разделом конфигурации.

`CreateDefaultBuilder` по умолчанию вызывает `Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    }
  }
}
```

## <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Настройте Kestrel для использования протокола HTTPS.

Расширения `ListenOptions.UseHttps`:

* `UseHttps`. Настройте Kestrel для использования протокола HTTPS с сертификатом по умолчанию. Создает исключение, если сертификат по умолчанию не настроен.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

Параметры `ListenOptions.UseHttps`:

* `filename` — это путь и имя файла сертификата, связанного с каталогом, где находятся файлы содержимого приложения.
* `password` — это пароль для доступа к данным сертификата X.509.
* `configureOptions` — это `Action` для настройки `HttpsConnectionAdapterOptions`. Возвращает `ListenOptions`.
* `storeName` — это хранилище сертификатов, из которого выполняется загрузка сертификата.
* `subject` — это имя субъекта для сертификата.
* `allowInvalid` указывает, следует ли учитывать недопустимые сертификаты, например самозаверяющие сертификаты.
* `location` — это расположение хранилища, из которого загружается сертификат.
* `serverCertificate` — это сертификат X.509.

В рабочей среде необходимо явно настроить HTTPS. Как минимум необходимо указать сертификат по умолчанию.

Поддерживаемые конфигурации, описанные далее:

* Отсутствие конфигурации
* Замена сертификата по умолчанию из конфигурации
* Изменение значений по умолчанию в коде

### <a name="no-configuration"></a>Отсутствие конфигурации

Kestrel ожидает передачи данных через `http://localhost:5000` и `https://localhost:5001` (если доступен сертификат по умолчанию).

<a name="configuration"></a>

### <a name="replace-the-default-certificate-from-configuration"></a>Замена сертификата по умолчанию из конфигурации

Kestrel имеет доступ к схеме конфигурации параметров приложения HTTPS по умолчанию. Настройте несколько конечных точек, включая URL-адреса и сертификаты для использования, либо из файла на диске, либо из хранилища сертификатов.

В следующем примере *appsettings.json* :

* Установите для `AllowInvalid` значение `true`, чтобы разрешить использование недопустимых сертификатов (например, самозаверяющих сертификатов).
* Любая конечная точка HTTPS, которая не указывает сертификат (`HttpsDefaultCert` в следующем примере), будет использовать сертификат, определенный в разделе `Certificates:Default`, или сертификат разработки.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertAndKeyFile": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Path": "<path to .pem/.crt file>",
          "KeyPath": "<path to .key file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5003",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5004"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Примечания к схеме.

* В имена конечных точек [регистр не учитывается](xref:fundamentals/configuration/index#configuration-keys-and-values). Например, выражение `HTTPS` and `Https` являются эквивалентными.
* Параметр `Url` является обязательным для каждой конечной точки. Формат этого параметра такой же, как для параметра конфигурации `Urls` верхнего уровня, только он ограничен одиночным значением.
* Эти конечные точки заменяют конечные точки, определенные в конфигурации `Urls` верхнего уровня, а не дополняют их. Конечные точки, определенные в коде через `Listen`, объединяются с конечными точками, определенными в разделе конфигурации.
* Раздел `Certificate` является необязательным. Если раздел `Certificate` не указан, используются значения по умолчанию, определенные в `Certificates:Default`. Если значения по умолчанию недоступны, используется сертификат разработки. Если значений по умолчанию нет и сертификат разработки отсутствует, сервер выдаст исключение и не сможет запуститься.
* Раздел `Certificate` поддерживает несколько [источников сертификатов](#certificate-sources).
* В [конфигурации](xref:fundamentals/configuration/index) можно определить любое количество конечных точек, если это не приводит к конфликту портов.

#### <a name="certificate-sources"></a>Источники сертификатов

Узлы сертификатов можно настроить для загрузки сертификатов из нескольких источников:

* `Path` и `Password` для загрузки файлов *.pfx*;
* `Path`, `KeyPath` и `Password` для загрузки файлов *.pem*/ *.crt* и *.key*;
* `Subject` и `Store` для загрузки из хранилища сертификатов.

Например, сертификат из раздела `Certificates:Default` можно указать следующим образом:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

#### <a name="configurationloader"></a>ConfigurationLoader

`options.Configure(context.Configuration.GetSection("{SECTION}"))` возвращает <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelConfigurationLoader> с методом `.Endpoint(string name, listenOptions => { })`, который может использоваться в качестве дополнения для параметров настроенной конечной точки:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

Можно обратиться напрямую к `KestrelServerOptions.ConfigurationLoader`, чтобы и далее выполнять итерацию с существующим загрузчиком, например, предоставленным <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.

* Раздел конфигурации для каждой конечной точки доступен в параметрах в методе `Endpoint`, чтобы можно было прочитать пользовательские параметры.
* Можно загрузить несколько конфигураций, снова вызвав `options.Configure(context.Configuration.GetSection("{SECTION}"))` с другим разделом. Используется только последняя конфигурация, если явным образом не вызвать `Load` в предыдущих экземплярах. Метапакет не вызывает `Load`, чтобы можно было заменить его раздел конфигурации по умолчанию.
* `KestrelConfigurationLoader` отражает семейство API `Listen` из `KestrelServerOptions` как перегрузки `Endpoint`, чтобы можно было настроить конечные точки кода и конфигурации в одном месте. Эти перегрузки не используют имена и используют только параметры по умолчанию из конфигурации.

### <a name="change-the-defaults-in-code"></a>Изменение значений по умолчанию в коде

Можно использовать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults` для изменения параметров по умолчанию для `ListenOptions` и `HttpsConnectionAdapterOptions`, включая переопределение сертификата по умолчанию, указанного в предыдущем сценарии. Необходимо вызвать `ConfigureEndpointDefaults` и `ConfigureHttpsDefaults`, прежде чем настраивать конечные точки.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

## <a name="configure-endpoints-using-server-name-indication"></a>Настройка конечных точек посредством указания имени сервера

Можно использовать [указание имени сервера (SNI)](https://tools.ietf.org/html/rfc6066#section-3) для размещения нескольких доменов в одном IP-адресе и порте. Для использования SNI клиент отправляет имя узла для безопасного сеанса серверу во время подтверждения TLS, чтобы сервер предоставил правильный сертификат. Клиент использует предоставленный сертификат для зашифрованного соединения с сервером во время безопасного сеанса, который следует после подтверждения TLS.

Kestrel поддерживает SNI через обратный вызов `ServerCertificateSelector`. Функция обратного вызова используется один раз за подключение, чтобы приложение проверило имя узла и выбрало соответствующий сертификат. Приведенный ниже код обратного вызова можно использовать в вызове метода `ConfigureWebHostDefaults` файла *Program.cs* проекта:

```csharp
//using System.Security.Cryptography.X509Certificates;
//using Microsoft.AspNetCore.Server.Kestrel.Https;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(StringComparer.OrdinalIgnoreCase)
            {
                { "localhost", localhostCert },
                { "example.com", exampleCert },
                { "sub.example.com", subExampleCert },
            };            

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

Поддержка SNI требует:

* Запуск на целевой платформе `netcoreapp2.1` или более поздней версии. В `net461` или более поздней версии обратный вызов выполняется, но `name` всегда имеет значение `null`. `name` также имеет значение `null`, если клиент не предоставляет параметр имени узла при подтверждении TLS.
* Все веб-сайты выполняются на одном и том же экземпляре Kestrel. Kestrel не поддерживает совместное использование IP-адреса и порта на нескольких экземплярах без обратного прокси-сервера.

## <a name="connection-logging"></a>Ведение журнала подключения

Вызовите <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging%2A>, чтобы выдать журналы уровня отладки для обмена данными на уровне байтов в рамках подключения. Ведение журнала подключения полезно для устранения неполадок, связанных с низкоуровневым взаимодействием, например при TLS-шифровании и работе за прокси-серверами. Если `UseConnectionLogging` поместить перед `UseHttps`, в журнале регистрируется зашифрованный трафик. Если `UseConnectionLogging` поместить после `UseHttps`, в журнале регистрируется расшифрованный трафик. Это встроенное [ПО промежуточного слоя подключения](#connection-middleware).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

## <a name="bind-to-a-tcp-socket"></a>Привязка к TCP-сокету

Метод <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen%2A> выполняет привязку к TCP-сокету, а лямбда-выражение параметров позволяет настроить конфигурацию сертификата X.509:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

В этом примере настраивается HTTPS для конечной точки с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. С помощью этого API можно настроить и другие параметры Kestrel для отдельных конечных точек.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

## <a name="bind-to-a-unix-socket"></a>Привязка к сокету UNIX

Вы можете прослушивать сокет UNIX с помощью <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A>, чтобы улучшить производительность Nginx, как показано в следующем примере:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* В файле конфигурации Nginx установите для записи `server` > `location` > `proxy_pass` значение `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}` — это имя сокета, предоставленного для <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket%2A> (как `kestrel-test.sock` в предыдущем примере).
* Убедитесь, что сокет доступен для записи Nginx (например, `chmod go+w /tmp/kestrel-test.sock`).

## <a name="port-0"></a>Порт 0

Если указать номер порта `0`, Kestrel динамически привязывается к доступному порту. Следующий пример показывает, как определить, к какому порту привязан Kestrel во время выполнения:

[!code-csharp[](samples/5.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Когда приложение выполняется, в выходных данных в окне консоли указывается динамический порт, по которому можно связаться с приложением:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

## <a name="limitations"></a>Ограничения

Настройте конечные точки с помощью следующих подходов:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls%2A>
* Аргументы командной строки `--urls`.
* Ключ конфигурации узла `urls`.
* Переменная среды `ASPNETCORE_URLS`.

Эти методы удобны, если нужно, чтобы код работал с серверами, отличными от Kestrel. Не забывайте о следующих ограничениях.

* С этими подходами нельзя использовать HTTPS, если в конфигурации конечной точки HTTPS не предоставлен сертификат по умолчанию (например, с помощью конфигурации `KestrelServerOptions` или файла конфигурации, как показано выше в этой статье).
* Если подходы `Listen` и `UseUrls` используются одновременно, конечные точки `Listen` переопределяют конечные точки `UseUrls`.

## <a name="iis-endpoint-configuration"></a>Конфигурация конечной точки IIS

При использовании служб IIS привязки URL-адресов для IIS переопределяют привязки, заданные `Listen` или `UseUrls`. Дополнительные сведения см. в статье [Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

## <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

Свойство `Protocols` устанавливает протоколы HTTP (`HttpProtocols`), разрешенные для конечной точки подключения или для сервера. Значение свойства `Protocols` должно входить в перечисление `HttpProtocols`.

| Значение перечисления `HttpProtocols` | Допустимый протокол подключения |
| -------------------------- | ----------------------------- |
| `Http1`                    | Только HTTP/1.1. Можно использовать с протоколом TLS или без него. |
| `Http2`                    | Только HTTP/2. Может использоваться без TLS только в том случае, если клиент поддерживает [режим предварительного знания](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 и HTTP/2. Для использования HTTP/2 требуется, чтобы клиент выбрал HTTP/2 при подтверждении [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3); в противном случае используется подключение по умолчанию HTTP/1.1. |

Значение `ListenOptions.Protocols` по умолчанию для любой конечной точки равно `HttpProtocols.Http1AndHttp2`.

Ограничения TLS для HTTP/2:

* TSL 1.2 или более поздней версии.
* Повторное согласование отключено.
* Сжатие отключено.
* Минимальные размеры обмена временными ключами:
  * эллиптическая кривая Диффи—Хелмана (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: не менее 224 бит;
  * конечное поле Диффи—Хелмана (DHE) &lbrack;`TLS12`&rbrack;: не менее 2048 бит.
* Набор шифров не запрещен. 

По умолчанию поддерживается `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; с эллиптической кривой P-256 &lbrack;`FIPS186`&rbrack;.

Следующий пример разрешает подключения HTTP/1.1 и HTTP/2 через порт 8000. Эти подключения шифруются по протоколу TLS с использованием предоставленного сертификата:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

В Linux для фильтрации подтверждений TLS по каждому соединению можно использовать <xref:System.Net.Security.CipherSuitesPolicy>:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

## <a name="connection-middleware"></a>ПО промежуточного слоя подключения

При необходимости можно использовать ПО промежуточного слоя подключения для фильтрации подтверждений TLS для каждого подключения по конкретным шифрам.

Следующий пример вызывает <xref:System.NotSupportedException> для любого алгоритма шифрования, который не поддерживается приложением. Также можно определить и сравнить [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) со списком приемлемых наборов шифров.

При использовании алгоритма шифрования [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) шифрование не используется.

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

Фильтрацию соединений также можно настроить с помощью лямбды <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

## <a name="set-the-http-protocol-from-configuration"></a>Установка протокола HTTP из конфигурации

`CreateDefaultBuilder` по умолчанию вызывает `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, чтобы загрузить конфигурацию Kestrel.

В следующем примере *appsettings.json* для всех конечных точек устанавливается протокол подключения по умолчанию HTTP/1.1:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

В следующем примере *appsettings.json* для отдельной конечной точки устанавливается протокол подключения HTTP/1.1:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Указанные в коде протоколы переопределяют значения, заданные в конфигурации.

## <a name="url-prefixes"></a>Префиксы URL-адресов

Если вы используете `UseUrls`, аргумент командной строки `--urls`, ключ конфигурации узла `urls` или переменную среды `ASPNETCORE_URLS`, префиксы URL-адресов могут иметь любой из указанных ниже форматов.

Допустимы только префиксы URL-адресов HTTP. Kestrel не поддерживает HTTP при настройке привязок URL-адресов с помощью `UseUrls`.

* IPv4-адрес с номером порта

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` является особым случаем, соответствующим привязке ко всем IPv4-адресам.

* IPv6-адрес с номером порта

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` является IPv6-аналогом IPv4-адреса `0.0.0.0`.

* Имя узла с номером порта

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Имена узлов, `*` и `+`, не являются особыми. Все, что не распознается как допустимый IP-адрес или `localhost`, привязывается ко всем IP-адресам IPv4 и IPv6. Чтобы привязать разные имена узлов к разным приложениям ASP.NET Core по одному порту, используйте [HTTP.sys](xref:fundamentals/servers/httpsys) или обратный прокси-сервер. В качестве обратного прокси-сервера можно использовать IIS, Nginx или Apache.

  > [!WARNING]
  > Для размещения в конфигурации обратного прокси-сервера требуется [фильтрация узлов](xref:fundamentals/servers/kestrel/host-filtering).

* Имя узла `localhost` с номером порта или IP-адрес замыкания на себя с номером порта

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Когда указан `localhost`, Kestrel пытается привязаться к обоим интерфейсам замыкания на себя IPv4 и IPv6. Если запрошенный порт уже используется другой службой в одном из интерфейсов замыкания на себя, Kestrel не запускается. Если один из интерфейсов замыкания на себя недоступен по любой другой причине (чаще всего, это отсутствие поддержки IPv6), Kestrel заносит в журнал предупреждение.
