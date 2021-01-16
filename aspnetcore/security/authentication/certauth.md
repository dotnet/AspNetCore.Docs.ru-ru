---
title: Настройка проверки подлинности сертификата в ASP.NET Core
author: blowdart
description: Узнайте, как настроить проверку подлинности сертификата в ASP.NET Core для IIS и HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 07/16/2020
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
uid: security/authentication/certauth
ms.openlocfilehash: 71f05163c075a2ef88d5c606814925cdcef879d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253050"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Настройка проверки подлинности сертификата в ASP.NET Core

`Microsoft.AspNetCore.Authentication.Certificate` содержит реализацию, похожую на [проверку подлинности с помощью сертификата](https://tools.ietf.org/html/rfc5246#section-7.4.4) для ASP.NET Core. Проверка подлинности по сертификату происходит на уровне TLS, задолго до его попадания в ASP.NET Core. Точнее, это обработчик проверки подлинности, который проверяет сертификат, а затем предоставляет событие, в котором можно разрешить этот сертификат в `ClaimsPrincipal` . 

[Настройте сервер](#configure-your-server-to-require-certificates) для проверки подлинности на основе сертификата, как IIS, Kestrel, веб-приложения Azure или любое другое приложение, которое вы используете.

## <a name="proxy-and-load-balancer-scenarios"></a>Сценарии прокси-сервера и подсистемы балансировки нагрузки

Проверка подлинности с помощью сертификата — это сценарий с отслеживанием состояния, который в основном используется, когда прокси или балансировщик нагрузки не обрабатывает трафик между клиентами и серверами. Если используется прокси-сервер или балансировщик нагрузки, проверка подлинности сертификата работает только в том случае, если прокси-сервер или балансировщик нагрузки:

* Обрабатывает проверку подлинности.
* Передает сведения о проверке подлинности пользователя в приложение (например, в заголовке запроса), которое действует на данные проверки подлинности.

Альтернативой для проверки подлинности на сертификат в средах, где используются прокси-серверы и подсистемы балансировки нагрузки, являются Active Directory Федеративные службы (ADFS) с OpenID Connect Connect (OIDC).

## <a name="get-started"></a>Начало работы

Получите HTTPS сертификат, примените его и [Настройте сервер](#configure-your-server-to-require-certificates) для использования сертификатов.

В веб-приложении добавьте ссылку на пакет [Microsoft. AspNetCore. Authentication. Certificate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Certificate) . Затем в `Startup.ConfigureServices` методе вызовите `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` с вашими параметрами, предоставив делегат для, `OnCertificateValidated` чтобы выполнить дополнительную проверку сертификата клиента, отправляемого с запросами. Включите эту информацию в `ClaimsPrincipal` и установите ее в `context.Principal` свойстве.

Если проверка подлинности завершается неудачно, этот обработчик возвращает `403 (Forbidden)` ответ, а не `401 (Unauthorized)` , как вы можете ожидать. Причина заключается в том, что проверка подлинности должна выполняться во время первоначального TLS-подключения. К моменту, когда он достигает обработчика, он слишком поздно. Невозможно обновить подключение между анонимным подключением и сертификатом.

Кроме того `app.UseAuthentication();` , добавьте в `Startup.Configure` метод. В противном случае `HttpContext.User` не будет задано значение, `ClaimsPrincipal` созданное на основе сертификата. Пример:

::: moniker range=">= aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate()
        // Adding an ICertificateValidationCache results in certificate auth caching the results.
        // The default implementation uses a memory cache.
        .AddCertificateCache();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate();

    // All other service configuration
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All other app configuration
}
```

::: moniker-end

В предыдущем примере показан способ добавления проверки подлинности с помощью сертификата по умолчанию. Обработчик конструирует субъект-пользователя, используя общие свойства сертификата.

## <a name="configure-certificate-validation"></a>Настройка проверки сертификата

`CertificateAuthenticationOptions`Обработчик содержит некоторые встроенные проверки, которые являются минимальными проверками, которые следует выполнить с сертификатом. Каждый из этих параметров включен по умолчанию.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>Алловедцертификатетипес = цепочка, Селфсигнед или все (цепочка | Селфсигнед)

Значение по умолчанию — `CertificateTypes.Chained`.

Эта проверка подтверждает, что разрешен только соответствующий тип сертификата. Если приложение использует самозаверяющие сертификаты, для этого параметра необходимо задать значение `CertificateTypes.All` или `CertificateTypes.SelfSigned` .

### <a name="validatecertificateuse"></a>валидатецертификатеусе

Значение по умолчанию — `true`.

Эта проверка проверяет, что сертификат, предоставленный клиентом, имеет расширенное использование ключа проверки подлинности клиента (EKU) или вообще не содержит EKU. Как говорится в спецификации, если EKU не указано, все EKU считаются допустимыми.

### <a name="validatevalidityperiod"></a>валидатевалидитипериод

Значение по умолчанию — `true`.

Эта проверка проверяет, что сертификат находится в пределах срока действия. В каждом запросе обработчик гарантирует, что сертификат, который был действителен, когда он был предоставлен, не истечет в течение текущего сеанса.

### <a name="revocationflag"></a>ревокатионфлаг

Значение по умолчанию — `X509RevocationFlag.ExcludeRoot`.

Флаг, указывающий, какие сертификаты в цепочке проверяются на отзыв.

Проверки отзыва выполняются только в том случае, если сертификат связан с корневым сертификатом.

### <a name="revocationmode"></a>ревокатионмоде

Значение по умолчанию — `X509RevocationMode.Online`.

Флаг, указывающий, как выполняются проверки отзыва.

Указание проверки в сети может привести к длительной задержке при обращении к центру сертификации.

Проверки отзыва выполняются только в том случае, если сертификат связан с корневым сертификатом.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Можно ли настроить в приложении требование сертификата только для определенных путей?

Это невозможно. Помните, что обмен сертификатами выполняется в начале диалога HTTPS, он выполняется сервером до получения первого запроса на это подключение, поэтому невозможно ограничить область на основе каких-либо полей запроса.

## <a name="handler-events"></a>События обработчика

Обработчик имеет два события:

* `OnAuthenticationFailed`: Вызывается, если исключение происходит во время проверки подлинности и позволяет реагировать.
* `OnCertificateValidated`: Вызывается после проверки сертификата, прошел проверку и создается участник по умолчанию. Это событие позволяет выполнять собственную проверку и дополнять или заменять субъект. Ниже приведены примеры.
  * Определение, известен ли сертификат службам.
  * Создание собственного участника. Рассмотрим следующий пример в `Startup.ConfigureServices`:

    ```csharp
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
        .AddCertificate(options =>
        {
            options.Events = new CertificateAuthenticationEvents
            {
                OnCertificateValidated = context =>
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(ClaimTypes.Name,
                            context.ClientCertificate.Subject,
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };
    
                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
    
                    return Task.CompletedTask;
                }
            };
        });
    ```

Если входящий сертификат не соответствует дополнительной проверке, позвоните `context.Fail("failure reason")` по причине сбоя.

Для реальной функциональности вам, вероятно, потребуется вызвать службу, зарегистрированную в внедрении зависимостей, которая подключается к базе данных или другому хранилищу пользователей. Получите доступ к службе с помощью контекста, переданного в делегат. Рассмотрим следующий пример в `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetRequiredService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

По сути, проверка сертификата является проблемой авторизации. Добавление проверки, например, издателя или отпечатка в политике авторизации, а не внутри `OnCertificateValidated` , вполне приемлемо.

## <a name="configure-your-server-to-require-certificates"></a>Настройка сервера для использования сертификатов

### <a name="kestrel"></a>Kestrel

В *Program.CS* настройте Kestrel следующим образом:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> К конечным точкам, созданным путем вызова <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **перед** вызовом <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*>, не будут применяться значения по умолчанию.

### <a name="iis"></a>IIS

В диспетчере служб IIS выполните следующие действия.

1. Выберите сайт на вкладке " **подключения** ".
1. Дважды щелкните параметр **Параметры SSL** в окне **просмотра функций** .
1. Установите флажок **требовать SSL** и установите переключатель **требуется** в разделе **Сертификаты клиента** .

![Параметры сертификата клиента в службах IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure и настраиваемые веб-прокси

Сведения о настройке по промежуточного слоя для переадресации сертификатов см. в [документации по узлу и развертыванию](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) .

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Использование проверки подлинности сертификата в веб-приложениях Azure

Для Azure Настройка пересылки не требуется. Это уже настроено в по промежуточного слоя на пересылку сертификатов.

> [!NOTE]
> Для этого необходимо наличие Цертификатефорвардингмиддлеваре.

### <a name="use-certificate-authentication-in-custom-web-proxies"></a>Использование проверки подлинности сертификатов в пользовательских веб-прокси

`AddCertificateForwarding`Метод используется для указания:

* Имя заголовка клиента.
* Способ загрузки сертификата (с помощью `HeaderConverter` Свойства).

В пользовательских веб-прокси сертификат передается в виде пользовательского заголовка запроса, например `X-SSL-CERT` . Чтобы использовать его, Настройте переадресацию сертификатов в `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-SSL-CERT";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;

            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

`Startup.Configure`Затем метод добавляет по промежуточного слоя. `UseCertificateForwarding` вызывается перед вызовами функций `UseAuthentication` и `UseAuthorization` :

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Для реализации логики проверки можно использовать отдельный класс. Так как в этом примере используется тот же самозаверяющий сертификат, убедитесь, что только ваш сертификат можно использовать. Убедитесь, что отпечатки сертификата клиента и сертификата сервера совпадают, в противном случае любой сертификат может быть использован и будет достаточно для проверки подлинности. Он будет использоваться внутри `AddCertificate` метода. Вы также можете проверить субъект или его издателя, если вы используете промежуточные или дочерние сертификаты.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-the-httpclienthandler"></a>Реализация HttpClient с помощью сертификата и HttpClientHandler

`HttpClientHandler`Можно добавить непосредственно в конструктор `HttpClient` класса. Будьте осторожны при создании экземпляров `HttpClient` . `HttpClient`Затем будет отправлен сертификат с каждым запросом.

```csharp
private async Task<JsonDocument> GetApiDataUsingHttpClientHandler()
{
    var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(cert);
    var client = new HttpClient(handler);
     
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

#### <a name="implement-an-httpclient-using-a-certificate-and-a-named-httpclient-from-ihttpclientfactory"></a>Реализация HttpClient с помощью сертификата и именованного HttpClient из Ихттпклиентфактори 

В следующем примере сертификат клиента добавляется в, `HttpClientHandler` используя `ClientCertificates` свойство из обработчика. Затем этот обработчик можно использовать в именованном экземпляре `HttpClient` с помощью `ConfigurePrimaryHttpMessageHandler` метода. Это программа установки в `Startup.ConfigureServices` .

```csharp
var clientCertificate = 
    new X509Certificate2(
      Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");
 
var handler = new HttpClientHandler();
handler.ClientCertificates.Add(clientCertificate);
 
services.AddHttpClient("namedClient", c =>
{
}).ConfigurePrimaryHttpMessageHandler(() => handler);
```

`IHttpClientFactory`Затем можно использовать для получения именованного экземпляра с обработчиком и сертификатом. `CreateClient`Для получения экземпляра используется метод с именем клиента, определенного в `Startup` классе. HTTP-запрос может быть отправлен с помощью клиента по мере необходимости.

```csharp
private readonly IHttpClientFactory _clientFactory;
 
public ApiService(IHttpClientFactory clientFactory)
{
    _clientFactory = clientFactory;
}
 
private async Task<JsonDocument> GetApiDataWithNamedClient()
{
    var client = _clientFactory.CreateClient("namedClient");
 
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://localhost:44379/api/values"),
        Method = HttpMethod.Get,
    };
    var response = await client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var responseContent = await response.Content.ReadAsStringAsync();
        var data = JsonDocument.Parse(responseContent);
        return data;
    }
 
    throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
}
```

Если на сервер отправляется правильный сертификат, возвращаются данные. Если сертификат или неправильный сертификат не отправляется, возвращается код состояния HTTP 403.

### <a name="create-certificates-in-powershell"></a>Создание сертификатов в PowerShell

Создание сертификатов — самая сложная часть настройки этого потока. Корневой сертификат можно создать с помощью `New-SelfSignedCertificate` командлета PowerShell. При создании сертификата используйте надежный пароль. Важно добавить `KeyUsageProperty` параметр и `KeyUsage` параметр, как показано ниже.

#### <a name="create-root-ca"></a>Создать корневой ЦС

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> `-DnsName`Значение параметра должно соответствовать целевому объекту развертывания приложения. Например, "localhost" для разработки.

#### <a name="install-in-the-trusted-root"></a>Установить в доверенном корневом каталоге

Корневой сертификат должен быть доверенным в системе узла. Корневой сертификат, который не был создан центром сертификации, не будет доверенным по умолчанию. Следующая ссылка объясняет, как это можно сделать в Windows:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Промежуточный сертификат

Теперь промежуточный сертификат можно создать на основе корневого сертификата. Это не является обязательным для всех вариантов использования, но может потребоваться создать несколько сертификатов или необходимо активировать или отключить группы сертификатов. `TextExtension`Параметр необходим для задания длины пути в базовых ограничениях сертификата.

Затем промежуточный сертификат можно добавить в доверенный промежуточный сертификат в системе узла Windows.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Создать дочерний сертификат из промежуточного сертификата

Дочерний сертификат можно создать из промежуточного сертификата. Это конечная сущность, и ей не нужно создавать больше дочерних сертификатов.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Создать дочерний сертификат из корневого сертификата

Дочерний сертификат можно также создать непосредственно из корневого сертификата. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Пример корневого промежуточного сертификата — сертификат

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

При использовании корневого, промежуточного или дочернего сертификата сертификаты можно проверять с помощью отпечатка или PublicKey по мере необходимости.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```

<a name="occ"></a>

::: moniker range=">= aspnetcore-5.0"

## <a name="certificate-validation-caching"></a>Кэширование проверки сертификатов

ASP.NET Core 5,0 и более поздних версий поддерживают возможность кэширования результатов проверки. Кэширование значительно повышает производительность проверки подлинности сертификата, так как проверка является дорогостоящей операцией.

По умолчанию проверка подлинности на сертификате отключает кэширование. Чтобы включить кэширование, вызовите `AddCertificateCache` в `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate()
            .AddCertificateCache(options =>
            {
                options.CacheSize = 1024;
                options.CacheEntryExpiration = TimeSpan.FromMinutes(2);
            });
}
```

Реализация кэширования по умолчанию сохраняет результаты в памяти. Вы можете предоставить собственный кэш, реализовав `ICertificateValidationCache` и зарегистрировав его с помощью внедрения зависимостей. Например, `services.AddSingleton<ICertificateValidationCache, YourCache>()`.

::: moniker-end

## <a name="optional-client-certificates"></a>Необязательные сертификаты клиента

В этом разделе содержатся сведения о приложениях, которые должны защищать подмножество приложения с помощью сертификата. Например, для Razor страницы или контроллера в приложении могут потребоваться сертификаты клиента. Это представляет проблемы в качестве сертификатов клиента:
  
* Являются компонентом TLS, а не компонентом HTTP.
* Согласовываются по подключению и должны быть согласованы в начале соединения, прежде чем будут доступны все данные HTTP. В начале соединения известен только указание имени сервера (SNI) &dagger; . Сертификаты клиента и сервера согласовываются до первого запроса в соединении, и запросы, как правило, не могут повторно согласовываться.

Повторное согласование TLS было старым способом реализации необязательных сертификатов клиента. Это больше не рекомендуется, так как:
- В HTTP/1.1 повторное согласование во время запроса POST может привести к взаимоблокировке, когда текст запроса заполнил окно TCP и пакеты повторного согласования не могут быть получены.
- HTTP/2 [явно запрещает](https://tools.ietf.org/html/rfc7540#section-9.2.1) повторное согласование.
- Протокол TLS 1,3 [удалил](https://tools.ietf.org/html/rfc8740#section-1) поддержку повторного согласования.

ASP.NET Core 5 Preview 7 и более поздней версии добавляет более удобную поддержку для необязательных клиентских сертификатов. Дополнительные сведения см. в [примере необязательных сертификатов](https://github.com/dotnet/aspnetcore/tree/9ce4a970a21bace3fb262da9591ed52359309592/src/Security/Authentication/Certificate/samples/Certificate.Optional.Sample).

Следующий подход поддерживает необязательные сертификаты клиента:

::: moniker range=">= aspnetcore-5.0"

* Настройка привязки для домена и поддомена:
  * Например, настройте привязки для `contoso.com` и `myClient.contoso.com` . `contoso.com`Узлу не требуется сертификат клиента, но он `myClient.contoso.com` выполняется.
  * Дополнительные сведения см. в разделе:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel/endpoints#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Примечание. Kestrel в настоящее время не поддерживает несколько конфигураций TLS для одной привязки, вам потребуются две привязки с уникальными IP-адресами или портами. См. раздел https://github.com/dotnet/runtime/issues/31097.
    * IIS
      * [Размещение IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Настройка безопасности служб IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Настройка Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Настройка привязки для домена и поддомена:
  * Например, настройте привязки для `contoso.com` и `myClient.contoso.com` . `contoso.com`Узлу не требуется сертификат клиента, но он `myClient.contoso.com` выполняется.
  * Дополнительные сведения см. в разделе:
    * [Kestrel](/fundamentals/servers/kestrel):
      * [ListenOptions.UseHttps](xref:fundamentals/servers/kestrel#listenoptionsusehttps)
      * <xref:Microsoft.AspNetCore.Server.Kestrel.Https.HttpsConnectionAdapterOptions.ClientCertificateMode>
      * Примечание. Kestrel в настоящее время не поддерживает несколько конфигураций TLS для одной привязки, вам потребуются две привязки с уникальными IP-адресами или портами. См. раздел https://github.com/dotnet/runtime/issues/31097.
    * IIS
      * [Размещение IIS](xref:host-and-deploy/iis/index#create-the-iis-site)
      * [Настройка безопасности служб IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#configure-ssl-settings-2)
    * Http.Sys: [Настройка Windows Server](xref:fundamentals/servers/httpsys#configure-windows-server)

::: moniker-end

* Для запросов к веб-приложению, которому требуется сертификат клиента, и у которых нет такого сертификата:
  * Перенаправление на ту же страницу с помощью защищенного поддомена сертификата клиента.
  * Например, перенаправление в `myClient.contoso.com/requestedPage` . Так как запрос на `myClient.contoso.com/requestedPage` имя узла отличается от `contoso.com/requestedPage` , клиент устанавливает другое соединение и предоставляется сертификат клиента.
  * Для получения дополнительной информации см. <xref:security/authorization/introduction>.

Оставьте вопросы, комментарии и другие отзывы о необязательных сертификатах клиентов в этой статье об [этом обсуждении GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/18720) .

&dagger; Указание имени сервера (SNI) — это расширение TLS для включения виртуального домена в состав согласования SSL. Это фактически означает, что имя виртуального домена или имени узла можно использовать для указания конечной точки сети.
