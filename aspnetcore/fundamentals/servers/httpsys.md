---
title: Реализация веб-сервера HTTP.sys в ASP.NET Core
author: rick-anderson
description: Общие сведения о веб-сервере HTTP.sys для ASP.NET Core в Windows. Веб-сервер HTTP.sys на основе работающего в режиме ядра драйвера Http.Sys — это альтернатива Kestrel, которую можно использовать для прямого подключения к Интернету без служб IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: ad37f8434b6025c5f3ec97dc52987f5660a64edc
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106678"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Реализация веб-сервера HTTP.sys в ASP.NET Core

Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Крис Росс](https://github.com/Tratcher) (Chris Ross)

::: moniker range=">= aspnetcore-3.1"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) — это [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index), который запускается только в Windows. HTTP.sys является альтернативой серверу [Kestrel](xref:fundamentals/servers/kestrel), предлагая некоторые функции, отсутствующие в Kestrel.

> [!IMPORTANT]
> HTTP.sys не подходит для использования с IIS или IIS Express из-за несовместимости с [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

HTTP.sys поддерживает следующие функции:

* [Аутентификация Windows](xref:security/authentication/windowsauth)
* Совместное использование портов
* Использование HTTPS с SNI
* Использование HTTP/2 через TLS (Windows 10 и более поздние версии)
* Прямая передача файлов
* Кэширование откликов
* Использование WebSockets (Windows 8 и более поздние версии)

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Условия для применения HTTP.sys

HTTP.sys удобно использовать с развертываниями в таких случаях:

* когда нужно подключить сервер к Интернету напрямую без использования служб IIS;

  ![HTTP.sys взаимодействует с Интернетом напрямую](httpsys/_static/httpsys-to-internet.png)

* когда для внутренних развертываний нужна функция, отсутствующая в Kestrel. Дополнительные сведения см. в разделе [Сравнение Kestrel и HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)

  ![HTTP.sys взаимодействует с внутренней сетью напрямую](httpsys/_static/httpsys-to-internal.png)

HTTP.sys — это проверенная технология, которая защищает от многих типов атак, а также обеспечивает надежность, безопасность и масштабируемость полнофункционального веб-сервера. Сами службы IIS выполняются в качестве HTTP-прослушивателя поверх HTTP.sys.

## <a name="http2-support"></a>Поддержка HTTP/2

Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) включен для приложений ASP.NET Core, если выполнены следующие базовые требования:

* установлена ОС Windows Server 2016 либо Windows 10 или более поздних версий;
* установлено подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3);
* Подключение TLS 1.2 или более поздней версии.

Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.

Протокол HTTP/2 включен по умолчанию. Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1. В будущих версиях Windows будут доступны флаги конфигурации HTTP/2, в том числе возможность отключения HTTP/2 с использованием HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Проверка подлинности в режиме ядра с помощью Kerberos

HTTP.sys делегирует задачи в проверку подлинности в режиме ядра с помощью протокола проверки подлинности Kerberos. Проверка подлинности в режиме пользователя не поддерживается с Kerberos и HTTP.sys. Необходимо использовать учетную запись компьютера для расшифровки маркера/билета Kerberos, полученного из Active Directory и переадресованного клиентом на сервер для проверки подлинности пользователя. Зарегистрируйте имя субъекта-службы (SPN) для узла, а не пользователя приложения.

## <a name="how-to-use-httpsys"></a>Способы применения HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Настройка приложения ASP.NET Core для использования HTTP.sys

Вызовите метод расширения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> при создании узла, указав все необходимые параметры <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>. В следующем примере для параметров задаются значения по умолчанию:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Дополнительная настройка HTTP.sys выполняется с помощью [параметров реестра](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Параметры HTTP.sys**

| Свойство. | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | Указывает, разрешен ли синхронные операции ввода-вывода для `HttpContext.Request.Body` и `HttpContext.Response.Body`. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Разрешает анонимные запросы. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Указывает разрешенные схемы аутентификации. Может быть изменен в любое время до удаления прослушивателя. Предоставляет значения, полученные при [перечислении AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` и `NTLM`. | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | Выполняет попытку кэшировать [режим ядра](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) для ответов с допустимыми заголовками. Ответ не может включать заголовки `Set-Cookie`, `Vary` или `Pragma`. Он должен включать заголовок `Cache-Control` со значением `public`, а также значение `shared-max-age` или `max-age` заголовок `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | Поведение HTTP.sys при отклонении запросов в соответствии с условиями регулирования. | [Http503VerbosityLevel.<br>Basic](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Максимальное число одновременных попыток. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Максимальное число попыток установить одновременное подключение. Использует `-1` для бесконечных циклов. Использует `null` для работы с параметром реестра на уровне компьютера. | `null`<br>(уровень компьютер<br>значение) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | См. раздел <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 байт.<br>(~28,6 МБ). |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Максимально допустимое число запросов в очереди. | 1000 |
| `RequestQueueMode` | Указывает, отвечает ли сервер за создание и настройку очереди запросов или он должен подключаться к существующей очереди.<br>Большинство имеющихся параметров конфигурации не применяются при подключении к существующей очереди. | `RequestQueueMode.Create` |
| `RequestQueueName` | Имя очереди запросов HTTP.sys. | `null` (анонимная очередь) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Указывает, следует ли вызывать исключение или завершать работу нормально, когда запись текста ответа завершается ошибкой из-за отключения клиента. | `false`<br>Нормальное завершение. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Предоставляет конфигурацию <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, которую также можно настроить в реестре. Дополнительные сведения о каждом параметре, включая значения по умолчанию, см. здесь:<ul><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType> — время, выделенное API сервера HTTP для очистки текста сущности при активном подключении.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType> — время, выделенное на получение текста сущности запроса.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType> — время, выделенное для API сервера HTTP для выполнения синтаксического анализа заголовка запроса.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType> — время, допустимое для неактивного подключения.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType> — минимальная скорость отправки ответа.</li><li><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType> — время, выделенное для пребывания запроса в очереди до его получения приложением.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Указывает <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> для регистрации с использованием HTTP.sys. Удобнее всего использовать параметр <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, который добавляет префикс к коллекции. Могут быть изменены в любое время до удаления прослушивателя. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Максимально допустимый размер текста запроса в байтах. Если задано значение `null`, размер максимального запроса не ограничен. Это ограничение не оказывает влияния на обновленные подключения, которые не имеют ограничений.

Чтобы переопределить это ограничение в приложении ASP.NET Core MVC для `IActionResult`, рекомендуется использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

При попытке приложения настроить ограничение для запроса после того, как приложение начало считывать запрос, возникает исключение. Свойство `IsReadOnly` указывает на то, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.

Если приложение должно переопределять <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> по запросу, используйте <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

При использовании Visual Studio убедитесь, что приложение не настроено для запуска IIS или IIS Express.

В Visual Studio профиль запуска по умолчанию использует IIS Express. Чтобы запустить проект как консольное приложение, измените выбранный профиль вручную, как показано на следующем снимке экрана.

![Выбор профиля консольного приложения](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Настройка Windows Server

1. Определите, какие порты нужно открыть для приложения, и используйте [брандмауэр Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) или командлет PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), чтобы открыть порты брандмауэра для доступа трафика к HTTP.sys. В следующих командах и конфигурации приложения используется порт 443.

1. При развертывании на виртуальных машинах Azure откройте эти порты в [группе безопасности сети](/azure/virtual-machines/windows/nsg-quickstart-portal). В следующих командах и конфигурации приложения используется порт 443.

1. При необходимости получите и установите сертификаты X.509.

   В Windows создайте самозаверяющие сертификаты с помощью [командлета PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Примеры, которые не поддерживаются, см. в разделе [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Установите самозаверяющие или подписанные центром сертификации сертификаты в хранилище сервера, выбрав **Локальный компьютер** > **Личный**.

1. Если приложение является [развертыванием, не зависящим от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd), установите NET Core или .NET Framework (или обе платформы, если это приложение .NET Core, предназначенное для .NET Framework).

   * **.NET Core**. Если приложению требуется .NET Core, загрузите установщик **среды выполнения .NET Core** на странице [скачивания .NET Core](https://dotnet.microsoft.com/download) и запустите его. Не устанавливайте полный пакет SDK на сервере.
   * **Платформа .NET Framework.** Если приложению требуется .NET Framework, см. [руководство по установке](/dotnet/framework/install/). Установите требуемую платформу .NET Framework. Установщик последней версии .NET Framework доступен на странице [скачивания .NET](https://dotnet.microsoft.com/download).

   Если приложение [развертывается автономно](/dotnet/core/deploying/#self-contained-deployments-scd), в его развертывание включена среда выполнения. Устанавливать .NET Framework на сервере не нужно.

1. Настройте URL-адреса и порты в приложении.

   По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Чтобы настроить префиксы URL-адресов и порты, используйте следующие параметры:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Аргументы командной строки `urls`.
   * Переменная среды `ASPNETCORE_URLS`.
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   В следующем примере кода показано, как использовать <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> с локальным IP-адресом сервера `10.0.0.4` через порт 443.

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Преимущество `UrlPrefixes` заключается в том, что при неправильном формате префиксов сразу же создается сообщение об ошибке.

   Этот параметр в `UrlPrefixes` переопределяет параметры `UseUrls`/`urls`/`ASPNETCORE_URLS`. Таким образом, преимущество переменных среды`UseUrls`, `urls`и `ASPNETCORE_URLS` заключается в возможности быстрого переключения между Kestrel и HTTP.sys.

   HTTP.sys использует [форматы строк UrlPrefix API сервера HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > **Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне. Они создают уязвимости и ставят под угрозу безопасность приложения. Сюда относятся и строгие, и нестрогие подстановочные знаки. Вместо подстановочных знаков используйте имена узлов или IP-адреса в явном виде. Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в стандарте [RFC 7230, раздел 5.4, Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Предварительно зарегистрируйте префиксы URL-адресов на сервере.

   Встроенным средством для настройки сервера HTTP.sys является *netsh.exe*. С помощью *netsh.exe* можно зарезервировать префиксы URL-адресов и назначить сертификаты X.509. Для использования этого средства требуются права администратора.

   Используйте средство *netsh.exe* для регистрации URL-адреса приложения.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`. Полное имя URL-адреса. Не используйте привязки с подстановочными знаками. Используйте допустимое имя узла или локальный IP-адрес. *URL-адрес должен включать косую черту в конце.*
   * `<USER>`. Определяет имя пользователя или группы пользователей.

   В следующем примере сервер имеет локальный IP-адрес `10.0.0.4`.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   При регистрации URL-адреса средство возвращает ответ `URL reservation successfully added`.

   Чтобы удалить зарегистрированный URL-адрес, используйте команду `delete urlacl`.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Зарегистрируйте сертификаты X.509 на сервере.

   Используйте средство *netsh.exe* для регистрации сертификатов приложения.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`. Задает локальный IP-адрес для привязки. Не используйте привязки с подстановочными знаками. Используйте допустимый IP-адрес.
   * `<PORT>`. Указывает порт для привязки.
   * `<THUMBPRINT>`. Отпечаток сертификата X.509.
   * `<GUID>`. Глобальный уникальный идентификатор приложения, задаваемый разработчиком в информационных целях.

   В справочных целях храните GUID в приложении в виде тега пакета.

   * В Visual Studio сделайте следующее:
     * Откройте свойства проекта приложения, щелкнув приложение правой кнопкой мыши в **обозревателе решений** и выбрав **Properties** (Свойства).
     * Перейдите на вкладку **Package** (Пакет).
     * Введите GUID, который вы указали в поле **Tags** (Теги).
   * Если Visual Studio не используется:
     * Откройте файл проекта приложения.
     * Добавьте свойство `<PackageTags>` в новую или существующую группу `<PropertyGroup>` с GUID, который вы создали.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   В следующем примере:

   * Локальный IP-адрес сервера — `10.0.0.4`.
   * Сетевой генератор случайных GUID задает значение `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   При регистрации сертификата средство возвращает ответ `SSL Certificate successfully added`.

   Чтобы удалить регистрацию сертификата, используйте команду `delete sslcert`.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Дополнительные сведения см. в справочной документации по *netsh.exe*:

   * [Команды netsh для протокола HTTP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Строки UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Запустите приложение.

   Если выполнена привязка к localhost через HTTP (не HTTPS) с номером порта больше 1024, для запуска приложения права администратора не требуются. При других конфигурациях (например, при использовании локального IP-адреса или привязки к порту 443) для запуска приложения требуются права администратора.

   Приложение отвечает по общедоступному IP-адресу сервера. В этом примере подключение к серверу происходит через Интернет по общедоступному IP-адресу `104.214.79.47` сервера.

   В этом примере используется сертификат разработки. После обхода предупреждения о ненадежном сертификате браузера происходит безопасная загрузка страницы.

   ![Окно браузера с отображаемой страницей индекса приложения](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для приложений, размещенных с помощью файла HTTP.sys, которые взаимодействуют с запросами из Интернета или корпоративной сети, может потребоваться дополнительная настройка при размещении за прокси-серверами и подсистемами балансировки нагрузки. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="advanced-http2-features-to-support-grpc"></a>Расширенные возможности HTTP/2 для поддержки gRPC

Дополнительные возможности HTTP/2 в HTTP.SYS поддерживают gRPC, включая трейлеры ответов и отправку кадров сброса.

Требования для выполнения gRPC в HTTP.sys:

* Сборка 19041.508 или более поздняя ОС Windows 10.
* Подключение TLS 1.2 или более поздней версии.

### <a name="trailers"></a>Трейлеры

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Включить проверку подлинности Windows с использованием HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API сервера HTTP](/windows/win32/http/http-api-start-page)
* [Репозиторий GitHub ASPNET/HttpSysServer (исходный код)](https://github.com/aspnet/HttpSysServer/)
* [Узел](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) — это [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index), который запускается только в Windows. HTTP.sys является альтернативой серверу [Kestrel](xref:fundamentals/servers/kestrel), предлагая некоторые функции, отсутствующие в Kestrel.

> [!IMPORTANT]
> HTTP.sys не подходит для использования с IIS или IIS Express из-за несовместимости с [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

HTTP.sys поддерживает следующие функции:

* [Аутентификация Windows](xref:security/authentication/windowsauth)
* Совместное использование портов
* Использование HTTPS с SNI
* Использование HTTP/2 через TLS (Windows 10 и более поздние версии)
* Прямая передача файлов
* Кэширование откликов
* Использование WebSockets (Windows 8 и более поздние версии)

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Условия для применения HTTP.sys

HTTP.sys удобно использовать с развертываниями в таких случаях:

* когда нужно подключить сервер к Интернету напрямую без использования служб IIS;

  ![HTTP.sys взаимодействует с Интернетом напрямую](httpsys/_static/httpsys-to-internet.png)

* когда для внутренних развертываний нужна функция, отсутствующая в Kestrel, например [аутентификация Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys взаимодействует с внутренней сетью напрямую](httpsys/_static/httpsys-to-internal.png)

HTTP.sys — это проверенная технология, которая защищает от многих типов атак, а также обеспечивает надежность, безопасность и масштабируемость полнофункционального веб-сервера. Сами службы IIS выполняются в качестве HTTP-прослушивателя поверх HTTP.sys.

## <a name="http2-support"></a>Поддержка HTTP/2

Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) включен для приложений ASP.NET Core, если выполнены следующие базовые требования:

* установлена ОС Windows Server 2016 либо Windows 10 или более поздних версий;
* установлено подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3);
* Подключение TLS 1.2 или более поздней версии.

Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.

Протокол HTTP/2 включен по умолчанию. Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1. В будущих версиях Windows будут доступны флаги конфигурации HTTP/2, в том числе возможность отключения HTTP/2 с использованием HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Проверка подлинности в режиме ядра с помощью Kerberos

HTTP.sys делегирует задачи в проверку подлинности в режиме ядра с помощью протокола проверки подлинности Kerberos. Проверка подлинности в режиме пользователя не поддерживается с Kerberos и HTTP.sys. Необходимо использовать учетную запись компьютера для расшифровки маркера/билета Kerberos, полученного из Active Directory и переадресованного клиентом на сервер для проверки подлинности пользователя. Зарегистрируйте имя субъекта-службы (SPN) для узла, а не пользователя приложения.

## <a name="how-to-use-httpsys"></a>Способы применения HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Настройка приложения ASP.NET Core для использования HTTP.sys

Вызовите метод расширения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> при создании узла, указав все необходимые параметры <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>. В следующем примере для параметров задаются значения по умолчанию:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Дополнительная настройка HTTP.sys выполняется с помощью [параметров реестра](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Параметры HTTP.sys**

| Свойство. | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Указывает, разрешен ли синхронные операции ввода-вывода для `HttpContext.Request.Body` и `HttpContext.Response.Body`. | `false` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Разрешает анонимные запросы. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Указывает разрешенные схемы аутентификации. Может быть изменен в любое время до удаления прослушивателя. Предоставляет значения, полученные при [перечислении AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` и `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Выполняет попытку кэшировать [режим ядра](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) для ответов с допустимыми заголовками. Ответ не может включать заголовки `Set-Cookie`, `Vary` или `Pragma`. Он должен включать заголовок `Cache-Control` со значением `public`, а также значение `shared-max-age` или `max-age` заголовок `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Максимальное число одновременных попыток. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Максимальное число попыток установить одновременное подключение. Использует `-1` для бесконечных циклов. Использует `null` для работы с параметром реестра на уровне компьютера. | `null`<br>(уровень компьютер<br>значение) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | См. раздел <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 байт.<br>(~28,6 МБ). |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Максимально допустимое число запросов в очереди. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Указывает, следует ли вызывать исключение или завершать работу нормально, когда запись текста ответа завершается ошибкой из-за отключения клиента. | `false`<br>Нормальное завершение. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Предоставляет конфигурацию <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, которую также можно настроить в реестре. Дополнительные сведения о каждом параметре, включая значения по умолчанию, см. здесь:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) — время, выделенное API сервера HTTP для очистки текста сущности при активном подключении.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) — время, выделенное на получение текста сущности запроса.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) — время, выделенное для API сервера HTTP для выполнения синтаксического анализа заголовка запроса.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) — время, допустимое для неактивного подключения.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) — минимальная скорость отправки ответа.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) — время, выделенное для пребывания запроса в очереди до его получения приложением.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Указывает <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> для регистрации с использованием HTTP.sys. Удобнее всего использовать параметр [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), который добавляет префикс к коллекции. Могут быть изменены в любое время до удаления прослушивателя. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Максимально допустимый размер текста запроса в байтах. Если задано значение `null`, размер максимального запроса не ограничен. Это ограничение не оказывает влияния на обновленные подключения, которые не имеют ограничений.

Чтобы переопределить это ограничение в приложении ASP.NET Core MVC для `IActionResult`, рекомендуется использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

При попытке приложения настроить ограничение для запроса после того, как приложение начало считывать запрос, возникает исключение. Свойство `IsReadOnly` указывает на то, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.

Если приложение должно переопределять <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> по запросу, используйте <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

При использовании Visual Studio убедитесь, что приложение не настроено для запуска IIS или IIS Express.

В Visual Studio профиль запуска по умолчанию использует IIS Express. Чтобы запустить проект как консольное приложение, измените выбранный профиль вручную, как показано на следующем снимке экрана.

![Выбор профиля консольного приложения](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Настройка Windows Server

1. Определите, какие порты нужно открыть для приложения, и используйте [брандмауэр Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) или командлет PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), чтобы открыть порты брандмауэра для доступа трафика к HTTP.sys. В следующих командах и конфигурации приложения используется порт 443.

1. При развертывании на виртуальных машинах Azure откройте эти порты в [группе безопасности сети](/azure/virtual-machines/windows/nsg-quickstart-portal). В следующих командах и конфигурации приложения используется порт 443.

1. При необходимости получите и установите сертификаты X.509.

   В Windows создайте самозаверяющие сертификаты с помощью [командлета PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Примеры, которые не поддерживаются, см. в разделе [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Установите самозаверяющие или подписанные центром сертификации сертификаты в хранилище сервера, выбрав **Локальный компьютер** > **Личный**.

1. Если приложение является [развертыванием, не зависящим от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd), установите NET Core или .NET Framework (или обе платформы, если это приложение .NET Core, предназначенное для .NET Framework).

   * **.NET Core**. Если приложению требуется .NET Core, загрузите установщик **среды выполнения .NET Core** на странице [скачивания .NET Core](https://dotnet.microsoft.com/download) и запустите его. Не устанавливайте полный пакет SDK на сервере.
   * **Платформа .NET Framework.** Если приложению требуется .NET Framework, см. [руководство по установке](/dotnet/framework/install/). Установите требуемую платформу .NET Framework. Установщик последней версии .NET Framework доступен на странице [скачивания .NET](https://dotnet.microsoft.com/download).

   Если приложение [развертывается автономно](/dotnet/core/deploying/#self-contained-deployments-scd), в его развертывание включена среда выполнения. Устанавливать .NET Framework на сервере не нужно.

1. Настройте URL-адреса и порты в приложении.

   По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Чтобы настроить префиксы URL-адресов и порты, используйте следующие параметры:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Аргументы командной строки `urls`.
   * Переменная среды `ASPNETCORE_URLS`.
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   В следующем примере кода показано, как использовать <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> с локальным IP-адресом сервера `10.0.0.4` через порт 443.

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Преимущество `UrlPrefixes` заключается в том, что при неправильном формате префиксов сразу же создается сообщение об ошибке.

   Этот параметр в `UrlPrefixes` переопределяет параметры `UseUrls`/`urls`/`ASPNETCORE_URLS`. Таким образом, преимущество переменных среды`UseUrls`, `urls`и `ASPNETCORE_URLS` заключается в возможности быстрого переключения между Kestrel и HTTP.sys.

   HTTP.sys использует [форматы строк UrlPrefix API сервера HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > **Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне. Они создают уязвимости и ставят под угрозу безопасность приложения. Сюда относятся и строгие, и нестрогие подстановочные знаки. Вместо подстановочных знаков используйте имена узлов или IP-адреса в явном виде. Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в стандарте [RFC 7230, раздел 5.4, Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Предварительно зарегистрируйте префиксы URL-адресов на сервере.

   Встроенным средством для настройки сервера HTTP.sys является *netsh.exe*. С помощью *netsh.exe* можно зарезервировать префиксы URL-адресов и назначить сертификаты X.509. Для использования этого средства требуются права администратора.

   Используйте средство *netsh.exe* для регистрации URL-адреса приложения.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`. Полное имя URL-адреса. Не используйте привязки с подстановочными знаками. Используйте допустимое имя узла или локальный IP-адрес. *URL-адрес должен включать косую черту в конце.*
   * `<USER>`. Определяет имя пользователя или группы пользователей.

   В следующем примере сервер имеет локальный IP-адрес `10.0.0.4`.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   При регистрации URL-адреса средство возвращает ответ `URL reservation successfully added`.

   Чтобы удалить зарегистрированный URL-адрес, используйте команду `delete urlacl`.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Зарегистрируйте сертификаты X.509 на сервере.

   Используйте средство *netsh.exe* для регистрации сертификатов приложения.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`. Задает локальный IP-адрес для привязки. Не используйте привязки с подстановочными знаками. Используйте допустимый IP-адрес.
   * `<PORT>`. Указывает порт для привязки.
   * `<THUMBPRINT>`. Отпечаток сертификата X.509.
   * `<GUID>`. Глобальный уникальный идентификатор приложения, задаваемый разработчиком в информационных целях.

   В справочных целях храните GUID в приложении в виде тега пакета.

   * В Visual Studio сделайте следующее:
     * Откройте свойства проекта приложения, щелкнув приложение правой кнопкой мыши в **обозревателе решений** и выбрав **Properties** (Свойства).
     * Перейдите на вкладку **Package** (Пакет).
     * Введите GUID, который вы указали в поле **Tags** (Теги).
   * Если Visual Studio не используется:
     * Откройте файл проекта приложения.
     * Добавьте свойство `<PackageTags>` в новую или существующую группу `<PropertyGroup>` с GUID, который вы создали.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   В следующем примере:

   * Локальный IP-адрес сервера — `10.0.0.4`.
   * Сетевой генератор случайных GUID задает значение `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   При регистрации сертификата средство возвращает ответ `SSL Certificate successfully added`.

   Чтобы удалить регистрацию сертификата, используйте команду `delete sslcert`.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Дополнительные сведения см. в справочной документации по *netsh.exe*:

   * [Команды netsh для протокола HTTP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Строки UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Запустите приложение.

   Если выполнена привязка к localhost через HTTP (не HTTPS) с номером порта больше 1024, для запуска приложения права администратора не требуются. При других конфигурациях (например, при использовании локального IP-адреса или привязки к порту 443) для запуска приложения требуются права администратора.

   Приложение отвечает по общедоступному IP-адресу сервера. В этом примере подключение к серверу происходит через Интернет по общедоступному IP-адресу `104.214.79.47` сервера.

   В этом примере используется сертификат разработки. После обхода предупреждения о ненадежном сертификате браузера происходит безопасная загрузка страницы.

   ![Окно браузера с отображаемой страницей индекса приложения](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для приложений, размещенных с помощью файла HTTP.sys, которые взаимодействуют с запросами из Интернета или корпоративной сети, может потребоваться дополнительная настройка при размещении за прокси-серверами и подсистемами балансировки нагрузки. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Включить проверку подлинности Windows с использованием HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API сервера HTTP](/windows/win32/http/http-api-start-page)
* [Репозиторий GitHub ASPNET/HttpSysServer (исходный код)](https://github.com/aspnet/HttpSysServer/)
* [Узел](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) — это [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index), который запускается только в Windows. HTTP.sys является альтернативой серверу [Kestrel](xref:fundamentals/servers/kestrel), предлагая некоторые функции, отсутствующие в Kestrel.

> [!IMPORTANT]
> HTTP.sys не подходит для использования с IIS или IIS Express из-за несовместимости с [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

HTTP.sys поддерживает следующие функции:

* [Аутентификация Windows](xref:security/authentication/windowsauth)
* Совместное использование портов
* Использование HTTPS с SNI
* Использование HTTP/2 через TLS (Windows 10 и более поздние версии)
* Прямая передача файлов
* Кэширование откликов
* Использование WebSockets (Windows 8 и более поздние версии)

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Условия для применения HTTP.sys

HTTP.sys удобно использовать с развертываниями в таких случаях:

* когда нужно подключить сервер к Интернету напрямую без использования служб IIS;

  ![HTTP.sys взаимодействует с Интернетом напрямую](httpsys/_static/httpsys-to-internet.png)

* когда для внутренних развертываний нужна функция, отсутствующая в Kestrel, например [аутентификация Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys взаимодействует с внутренней сетью напрямую](httpsys/_static/httpsys-to-internal.png)

HTTP.sys — это проверенная технология, которая защищает от многих типов атак, а также обеспечивает надежность, безопасность и масштабируемость полнофункционального веб-сервера. Сами службы IIS выполняются в качестве HTTP-прослушивателя поверх HTTP.sys.

## <a name="http2-support"></a>Поддержка HTTP/2

Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) включен для приложений ASP.NET Core, если выполнены следующие базовые требования:

* установлена ОС Windows Server 2016 либо Windows 10 или более поздних версий;
* установлено подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3);
* Подключение TLS 1.2 или более поздней версии.

Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`.

Протокол HTTP/2 включен по умолчанию. Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1. В будущих версиях Windows будут доступны флаги конфигурации HTTP/2, в том числе возможность отключения HTTP/2 с использованием HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Проверка подлинности в режиме ядра с помощью Kerberos

HTTP.sys делегирует задачи в проверку подлинности в режиме ядра с помощью протокола проверки подлинности Kerberos. Проверка подлинности в режиме пользователя не поддерживается с Kerberos и HTTP.sys. Необходимо использовать учетную запись компьютера для расшифровки маркера/билета Kerberos, полученного из Active Directory и переадресованного клиентом на сервер для проверки подлинности пользователя. Зарегистрируйте имя субъекта-службы (SPN) для узла, а не пользователя приложения.

## <a name="how-to-use-httpsys"></a>Способы применения HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Настройка приложения ASP.NET Core для использования HTTP.sys

Ссылка на пакет в файле проекта не требуется при использовании [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Если метапакет `Microsoft.AspNetCore.App` не используется, добавьте ссылку на пакет в файл [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Вызовите метод расширения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> при создании узла, указав все необходимые параметры <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>. В следующем примере для параметров задаются значения по умолчанию:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Дополнительная настройка HTTP.sys выполняется с помощью [параметров реестра](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Параметры HTTP.sys**

| Свойство. | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Указывает, разрешен ли синхронные операции ввода-вывода для `HttpContext.Request.Body` и `HttpContext.Response.Body`. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Разрешает анонимные запросы. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Указывает разрешенные схемы аутентификации. Может быть изменен в любое время до удаления прослушивателя. Предоставляет значения, полученные при [перечислении AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` и `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Выполняет попытку кэшировать [режим ядра](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) для ответов с допустимыми заголовками. Ответ не может включать заголовки `Set-Cookie`, `Vary` или `Pragma`. Он должен включать заголовок `Cache-Control` со значением `public`, а также значение `shared-max-age` или `max-age` заголовок `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Максимальное число одновременных попыток. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Максимальное число попыток установить одновременное подключение. Использует `-1` для бесконечных циклов. Использует `null` для работы с параметром реестра на уровне компьютера. | `null`<br>(уровень компьютер<br>значение) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | См. раздел <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 байт.<br>(~28,6 МБ). |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Максимально допустимое число запросов в очереди. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Указывает, следует ли вызывать исключение или завершать работу нормально, когда запись текста ответа завершается ошибкой из-за отключения клиента. | `false`<br>Нормальное завершение. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Предоставляет конфигурацию <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, которую также можно настроить в реестре. Дополнительные сведения о каждом параметре, включая значения по умолчанию, см. здесь:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) — время, выделенное API сервера HTTP для очистки текста сущности при активном подключении.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) — время, выделенное на получение текста сущности запроса.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) — время, выделенное для API сервера HTTP для выполнения синтаксического анализа заголовка запроса.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) — время, допустимое для неактивного подключения.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) — минимальная скорость отправки ответа.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) — время, выделенное для пребывания запроса в очереди до его получения приложением.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Указывает <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> для регистрации с использованием HTTP.sys. Удобнее всего использовать параметр [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), который добавляет префикс к коллекции. Могут быть изменены в любое время до удаления прослушивателя. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Максимально допустимый размер текста запроса в байтах. Если задано значение `null`, размер максимального запроса не ограничен. Это ограничение не оказывает влияния на обновленные подключения, которые не имеют ограничений.

Чтобы переопределить это ограничение в приложении ASP.NET Core MVC для `IActionResult`, рекомендуется использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

При попытке приложения настроить ограничение для запроса после того, как приложение начало считывать запрос, возникает исключение. Свойство `IsReadOnly` указывает на то, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.

Если приложение должно переопределять <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> по запросу, используйте <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

При использовании Visual Studio убедитесь, что приложение не настроено для запуска IIS или IIS Express.

В Visual Studio профиль запуска по умолчанию использует IIS Express. Чтобы запустить проект как консольное приложение, измените выбранный профиль вручную, как показано на следующем снимке экрана.

![Выбор профиля консольного приложения](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Настройка Windows Server

1. Определите, какие порты нужно открыть для приложения, и используйте [брандмауэр Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) или командлет PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), чтобы открыть порты брандмауэра для доступа трафика к HTTP.sys. В следующих командах и конфигурации приложения используется порт 443.

1. При развертывании на виртуальных машинах Azure откройте эти порты в [группе безопасности сети](/azure/virtual-machines/windows/nsg-quickstart-portal). В следующих командах и конфигурации приложения используется порт 443.

1. При необходимости получите и установите сертификаты X.509.

   В Windows создайте самозаверяющие сертификаты с помощью [командлета PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Примеры, которые не поддерживаются, см. в разделе [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Установите самозаверяющие или подписанные центром сертификации сертификаты в хранилище сервера, выбрав **Локальный компьютер** > **Личный**.

1. Если приложение является [развертыванием, не зависящим от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd), установите NET Core или .NET Framework (или обе платформы, если это приложение .NET Core, предназначенное для .NET Framework).

   * **.NET Core**. Если приложению требуется .NET Core, загрузите установщик **среды выполнения .NET Core** на странице [скачивания .NET Core](https://dotnet.microsoft.com/download) и запустите его. Не устанавливайте полный пакет SDK на сервере.
   * **Платформа .NET Framework.** Если приложению требуется .NET Framework, см. [руководство по установке](/dotnet/framework/install/). Установите требуемую платформу .NET Framework. Установщик последней версии .NET Framework доступен на странице [скачивания .NET](https://dotnet.microsoft.com/download).

   Если приложение [развертывается автономно](/dotnet/core/deploying/#self-contained-deployments-scd), в его развертывание включена среда выполнения. Устанавливать .NET Framework на сервере не нужно.

1. Настройте URL-адреса и порты в приложении.

   По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Чтобы настроить префиксы URL-адресов и порты, используйте следующие параметры:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Аргументы командной строки `urls`.
   * Переменная среды `ASPNETCORE_URLS`.
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   В следующем примере кода показано, как использовать <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> с локальным IP-адресом сервера `10.0.0.4` через порт 443.

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Преимущество `UrlPrefixes` заключается в том, что при неправильном формате префиксов сразу же создается сообщение об ошибке.

   Этот параметр в `UrlPrefixes` переопределяет параметры `UseUrls`/`urls`/`ASPNETCORE_URLS`. Таким образом, преимущество переменных среды`UseUrls`, `urls`и `ASPNETCORE_URLS` заключается в возможности быстрого переключения между Kestrel и HTTP.sys.

   HTTP.sys использует [форматы строк UrlPrefix API сервера HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > **Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне. Они создают уязвимости и ставят под угрозу безопасность приложения. Сюда относятся и строгие, и нестрогие подстановочные знаки. Вместо подстановочных знаков используйте имена узлов или IP-адреса в явном виде. Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в стандарте [RFC 7230, раздел 5.4, Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Предварительно зарегистрируйте префиксы URL-адресов на сервере.

   Встроенным средством для настройки сервера HTTP.sys является *netsh.exe*. С помощью *netsh.exe* можно зарезервировать префиксы URL-адресов и назначить сертификаты X.509. Для использования этого средства требуются права администратора.

   Используйте средство *netsh.exe* для регистрации URL-адреса приложения.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`. Полное имя URL-адреса. Не используйте привязки с подстановочными знаками. Используйте допустимое имя узла или локальный IP-адрес. *URL-адрес должен включать косую черту в конце.*
   * `<USER>`. Определяет имя пользователя или группы пользователей.

   В следующем примере сервер имеет локальный IP-адрес `10.0.0.4`.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   При регистрации URL-адреса средство возвращает ответ `URL reservation successfully added`.

   Чтобы удалить зарегистрированный URL-адрес, используйте команду `delete urlacl`.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Зарегистрируйте сертификаты X.509 на сервере.

   Используйте средство *netsh.exe* для регистрации сертификатов приложения.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`. Задает локальный IP-адрес для привязки. Не используйте привязки с подстановочными знаками. Используйте допустимый IP-адрес.
   * `<PORT>`. Указывает порт для привязки.
   * `<THUMBPRINT>`. Отпечаток сертификата X.509.
   * `<GUID>`. Глобальный уникальный идентификатор приложения, задаваемый разработчиком в информационных целях.

   В справочных целях храните GUID в приложении в виде тега пакета.

   * В Visual Studio сделайте следующее:
     * Откройте свойства проекта приложения, щелкнув приложение правой кнопкой мыши в **обозревателе решений** и выбрав **Properties** (Свойства).
     * Перейдите на вкладку **Package** (Пакет).
     * Введите GUID, который вы указали в поле **Tags** (Теги).
   * Если Visual Studio не используется:
     * Откройте файл проекта приложения.
     * Добавьте свойство `<PackageTags>` в новую или существующую группу `<PropertyGroup>` с GUID, который вы создали.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   В следующем примере:

   * Локальный IP-адрес сервера — `10.0.0.4`.
   * Сетевой генератор случайных GUID задает значение `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   При регистрации сертификата средство возвращает ответ `SSL Certificate successfully added`.

   Чтобы удалить регистрацию сертификата, используйте команду `delete sslcert`.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Дополнительные сведения см. в справочной документации по *netsh.exe*:

   * [Команды netsh для протокола HTTP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Строки UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Запустите приложение.

   Если выполнена привязка к localhost через HTTP (не HTTPS) с номером порта больше 1024, для запуска приложения права администратора не требуются. При других конфигурациях (например, при использовании локального IP-адреса или привязки к порту 443) для запуска приложения требуются права администратора.

   Приложение отвечает по общедоступному IP-адресу сервера. В этом примере подключение к серверу происходит через Интернет по общедоступному IP-адресу `104.214.79.47` сервера.

   В этом примере используется сертификат разработки. После обхода предупреждения о ненадежном сертификате браузера происходит безопасная загрузка страницы.

   ![Окно браузера с отображаемой страницей индекса приложения](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для приложений, размещенных с помощью файла HTTP.sys, которые взаимодействуют с запросами из Интернета или корпоративной сети, может потребоваться дополнительная настройка при размещении за прокси-серверами и подсистемами балансировки нагрузки. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Включить проверку подлинности Windows с использованием HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API сервера HTTP](/windows/win32/http/http-api-start-page)
* [Репозиторий GitHub ASPNET/HttpSysServer (исходный код)](https://github.com/aspnet/HttpSysServer/)
* [Узел](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) — это [веб-сервер для ASP.NET Core](xref:fundamentals/servers/index), который запускается только в Windows. HTTP.sys является альтернативой серверу [Kestrel](xref:fundamentals/servers/kestrel), предлагая некоторые функции, отсутствующие в Kestrel.

> [!IMPORTANT]
> HTTP.sys не подходит для использования с IIS или IIS Express из-за несовместимости с [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

HTTP.sys поддерживает следующие функции:

* [Аутентификация Windows](xref:security/authentication/windowsauth)
* Совместное использование портов
* Использование HTTPS с SNI
* Использование HTTP/2 через TLS (Windows 10 и более поздние версии)
* Прямая передача файлов
* Кэширование откликов
* Использование WebSockets (Windows 8 и более поздние версии)

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Условия для применения HTTP.sys

HTTP.sys удобно использовать с развертываниями в таких случаях:

* когда нужно подключить сервер к Интернету напрямую без использования служб IIS;

  ![HTTP.sys взаимодействует с Интернетом напрямую](httpsys/_static/httpsys-to-internet.png)

* когда для внутренних развертываний нужна функция, отсутствующая в Kestrel, например [аутентификация Windows](xref:security/authentication/windowsauth).

  ![HTTP.sys взаимодействует с внутренней сетью напрямую](httpsys/_static/httpsys-to-internal.png)

HTTP.sys — это проверенная технология, которая защищает от многих типов атак, а также обеспечивает надежность, безопасность и масштабируемость полнофункционального веб-сервера. Сами службы IIS выполняются в качестве HTTP-прослушивателя поверх HTTP.sys.

## <a name="http2-support"></a>Поддержка HTTP/2

Протокол [HTTP/2](https://httpwg.org/specs/rfc7540.html) включен для приложений ASP.NET Core, если выполнены следующие базовые требования:

* установлена ОС Windows Server 2016 либо Windows 10 или более поздних версий;
* установлено подключение с поддержкой [согласования протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3);
* Подключение TLS 1.2 или более поздней версии.

Если установлено подключение HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/1.1`.

Протокол HTTP/2 включен по умолчанию. Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1. В будущих версиях Windows будут доступны флаги конфигурации HTTP/2, в том числе возможность отключения HTTP/2 с использованием HTTP.sys.

## <a name="kernel-mode-authentication-with-kerberos"></a>Проверка подлинности в режиме ядра с помощью Kerberos

HTTP.sys делегирует задачи в проверку подлинности в режиме ядра с помощью протокола проверки подлинности Kerberos. Проверка подлинности в режиме пользователя не поддерживается с Kerberos и HTTP.sys. Необходимо использовать учетную запись компьютера для расшифровки маркера/билета Kerberos, полученного из Active Directory и переадресованного клиентом на сервер для проверки подлинности пользователя. Зарегистрируйте имя субъекта-службы (SPN) для узла, а не пользователя приложения.

## <a name="how-to-use-httpsys"></a>Способы применения HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Настройка приложения ASP.NET Core для использования HTTP.sys

Ссылка на пакет в файле проекта не требуется при использовании [метапакета Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)). Если метапакет `Microsoft.AspNetCore.App` не используется, добавьте ссылку на пакет в файл [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).

Вызовите метод расширения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> при создании узла, указав все необходимые параметры <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>. В следующем примере для параметров задаются значения по умолчанию:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Дополнительная настройка HTTP.sys выполняется с помощью [параметров реестра](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

**Параметры HTTP.sys**

| Свойство. | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Указывает, разрешен ли синхронные операции ввода-вывода для `HttpContext.Request.Body` и `HttpContext.Response.Body`. | `true` |
| [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Разрешает анонимные запросы. | `true` |
| [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Указывает разрешенные схемы аутентификации. Может быть изменен в любое время до удаления прослушивателя. Предоставляет значения, полученные при [перечислении AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` и `NTLM`. | `None` |
| [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Выполняет попытку кэшировать [режим ядра](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) для ответов с допустимыми заголовками. Ответ не может включать заголовки `Set-Cookie`, `Vary` или `Pragma`. Он должен включать заголовок `Cache-Control` со значением `public`, а также значение `shared-max-age` или `max-age` заголовок `Expires`. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Максимальное число одновременных попыток. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Максимальное число попыток установить одновременное подключение. Использует `-1` для бесконечных циклов. Использует `null` для работы с параметром реестра на уровне компьютера. | `null`<br>(уровень компьютер<br>значение) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | См. раздел <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30 000 000 байт.<br>(~28,6 МБ). |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Максимально допустимое число запросов в очереди. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Указывает, следует ли вызывать исключение или завершать работу нормально, когда запись текста ответа завершается ошибкой из-за отключения клиента. | `false`<br>Нормальное завершение. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Предоставляет конфигурацию <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> HTTP.sys, которую также можно настроить в реестре. Дополнительные сведения о каждом параметре, включая значения по умолчанию, см. здесь:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) — время, выделенное API сервера HTTP для очистки текста сущности при активном подключении.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) — время, выделенное на получение текста сущности запроса.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) — время, выделенное для API сервера HTTP для выполнения синтаксического анализа заголовка запроса.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) — время, допустимое для неактивного подключения.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) — минимальная скорость отправки ответа.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) — время, выделенное для пребывания запроса в очереди до его получения приложением.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Указывает <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> для регистрации с использованием HTTP.sys. Удобнее всего использовать параметр [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), который добавляет префикс к коллекции. Могут быть изменены в любое время до удаления прослушивателя. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Максимально допустимый размер текста запроса в байтах. Если задано значение `null`, размер максимального запроса не ограничен. Это ограничение не оказывает влияния на обновленные подключения, которые не имеют ограничений.

Чтобы переопределить это ограничение в приложении ASP.NET Core MVC для `IActionResult`, рекомендуется использовать атрибут <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> в методе действия:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

При попытке приложения настроить ограничение для запроса после того, как приложение начало считывать запрос, возникает исключение. Свойство `IsReadOnly` указывает на то, что свойство `MaxRequestBodySize` находится в состоянии только для чтения и настраивать ограничение слишком поздно.

Если приложение должно переопределять <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> по запросу, используйте <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

При использовании Visual Studio убедитесь, что приложение не настроено для запуска IIS или IIS Express.

В Visual Studio профиль запуска по умолчанию использует IIS Express. Чтобы запустить проект как консольное приложение, измените выбранный профиль вручную, как показано на следующем снимке экрана.

![Выбор профиля консольного приложения](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Настройка Windows Server

1. Определите, какие порты нужно открыть для приложения, и используйте [брандмауэр Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) или командлет PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), чтобы открыть порты брандмауэра для доступа трафика к HTTP.sys. В следующих командах и конфигурации приложения используется порт 443.

1. При развертывании на виртуальных машинах Azure откройте эти порты в [группе безопасности сети](/azure/virtual-machines/windows/nsg-quickstart-portal). В следующих командах и конфигурации приложения используется порт 443.

1. При необходимости получите и установите сертификаты X.509.

   В Windows создайте самозаверяющие сертификаты с помощью [командлета PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Примеры, которые не поддерживаются, см. в разделе [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Установите самозаверяющие или подписанные центром сертификации сертификаты в хранилище сервера, выбрав **Локальный компьютер** > **Личный**.

1. Если приложение является [развертыванием, не зависящим от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd), установите NET Core или .NET Framework (или обе платформы, если это приложение .NET Core, предназначенное для .NET Framework).

   * **.NET Core**. Если приложению требуется .NET Core, загрузите установщик **среды выполнения .NET Core** на странице [скачивания .NET Core](https://dotnet.microsoft.com/download) и запустите его. Не устанавливайте полный пакет SDK на сервере.
   * **Платформа .NET Framework.** Если приложению требуется .NET Framework, см. [руководство по установке](/dotnet/framework/install/). Установите требуемую платформу .NET Framework. Установщик последней версии .NET Framework доступен на странице [скачивания .NET](https://dotnet.microsoft.com/download).

   Если приложение [развертывается автономно](/dotnet/core/deploying/#self-contained-deployments-scd), в его развертывание включена среда выполнения. Устанавливать .NET Framework на сервере не нужно.

1. Настройте URL-адреса и порты в приложении.

   По умолчанию платформа ASP.NET Core привязана к `http://localhost:5000`. Чтобы настроить префиксы URL-адресов и порты, используйте следующие параметры:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Аргументы командной строки `urls`.
   * Переменная среды `ASPNETCORE_URLS`.
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   В следующем примере кода показано, как использовать <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> с локальным IP-адресом сервера `10.0.0.4` через порт 443.

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Преимущество `UrlPrefixes` заключается в том, что при неправильном формате префиксов сразу же создается сообщение об ошибке.

   Этот параметр в `UrlPrefixes` переопределяет параметры `UseUrls`/`urls`/`ASPNETCORE_URLS`. Таким образом, преимущество переменных среды`UseUrls`, `urls`и `ASPNETCORE_URLS` заключается в возможности быстрого переключения между Kestrel и HTTP.sys.

   HTTP.sys использует [форматы строк UrlPrefix API сервера HTTP](/windows/win32/http/urlprefix-strings).

   > [!WARNING]
   > **Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне. Они создают уязвимости и ставят под угрозу безопасность приложения. Сюда относятся и строгие, и нестрогие подстановочные знаки. Вместо подстановочных знаков используйте имена узлов или IP-адреса в явном виде. Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в стандарте [RFC 7230, раздел 5.4, Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Предварительно зарегистрируйте префиксы URL-адресов на сервере.

   Встроенным средством для настройки сервера HTTP.sys является *netsh.exe*. С помощью *netsh.exe* можно зарезервировать префиксы URL-адресов и назначить сертификаты X.509. Для использования этого средства требуются права администратора.

   Используйте средство *netsh.exe* для регистрации URL-адреса приложения.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`. Полное имя URL-адреса. Не используйте привязки с подстановочными знаками. Используйте допустимое имя узла или локальный IP-адрес. *URL-адрес должен включать косую черту в конце.*
   * `<USER>`. Определяет имя пользователя или группы пользователей.

   В следующем примере сервер имеет локальный IP-адрес `10.0.0.4`.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   При регистрации URL-адреса средство возвращает ответ `URL reservation successfully added`.

   Чтобы удалить зарегистрированный URL-адрес, используйте команду `delete urlacl`.

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Зарегистрируйте сертификаты X.509 на сервере.

   Используйте средство *netsh.exe* для регистрации сертификатов приложения.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`. Задает локальный IP-адрес для привязки. Не используйте привязки с подстановочными знаками. Используйте допустимый IP-адрес.
   * `<PORT>`. Указывает порт для привязки.
   * `<THUMBPRINT>`. Отпечаток сертификата X.509.
   * `<GUID>`. Глобальный уникальный идентификатор приложения, задаваемый разработчиком в информационных целях.

   В справочных целях храните GUID в приложении в виде тега пакета.

   * В Visual Studio сделайте следующее:
     * Откройте свойства проекта приложения, щелкнув приложение правой кнопкой мыши в **обозревателе решений** и выбрав **Properties** (Свойства).
     * Перейдите на вкладку **Package** (Пакет).
     * Введите GUID, который вы указали в поле **Tags** (Теги).
   * Если Visual Studio не используется:
     * Откройте файл проекта приложения.
     * Добавьте свойство `<PackageTags>` в новую или существующую группу `<PropertyGroup>` с GUID, который вы создали.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   В следующем примере:

   * Локальный IP-адрес сервера — `10.0.0.4`.
   * Сетевой генератор случайных GUID задает значение `appid`.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   При регистрации сертификата средство возвращает ответ `SSL Certificate successfully added`.

   Чтобы удалить регистрацию сертификата, используйте команду `delete sslcert`.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Дополнительные сведения см. в справочной документации по *netsh.exe*:

   * [Команды netsh для протокола HTTP](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))
   * [Строки UrlPrefix](/windows/win32/http/urlprefix-strings)

1. Запустите приложение.

   Если выполнена привязка к localhost через HTTP (не HTTPS) с номером порта больше 1024, для запуска приложения права администратора не требуются. При других конфигурациях (например, при использовании локального IP-адреса или привязки к порту 443) для запуска приложения требуются права администратора.

   Приложение отвечает по общедоступному IP-адресу сервера. В этом примере подключение к серверу происходит через Интернет по общедоступному IP-адресу `104.214.79.47` сервера.

   В этом примере используется сертификат разработки. После обхода предупреждения о ненадежном сертификате браузера происходит безопасная загрузка страницы.

   ![Окно браузера с отображаемой страницей индекса приложения](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Сценарии использования прокси-сервера и подсистемы балансировки нагрузки

Для приложений, размещенных с помощью файла HTTP.sys, которые взаимодействуют с запросами из Интернета или корпоративной сети, может потребоваться дополнительная настройка при размещении за прокси-серверами и подсистемами балансировки нагрузки. Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Включить проверку подлинности Windows с использованием HTTP.sys](xref:security/authentication/windowsauth#httpsys)
* [API сервера HTTP](/windows/win32/http/http-api-start-page)
* [Репозиторий GitHub ASPNET/HttpSysServer (исходный код)](https://github.com/aspnet/HttpSysServer/)
* [Узел](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
