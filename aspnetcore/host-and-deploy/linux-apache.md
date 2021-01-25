---
title: Размещение ASP.NET Core в операционной системе Linux с Apache
author: rick-anderson
description: Процедура настройки Apache в качестве обратного прокси-сервера в CentOS для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
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
uid: host-and-deploy/linux-apache
ms.openlocfilehash: e81ad43e1c3b86900848671d9da377a5c04a2a82
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253011"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Размещение ASP.NET Core в операционной системе Linux с Apache

Автор: [Шейн Бойер (Shayne Boyer)](https://github.com/spboyer)

Из этого руководства вы узнаете, как настроить [Apache](https://httpd.apache.org/) в качестве обратного прокси-сервера в [CentOS 7](https://www.centos.org/) для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое на сервере [Kestrel](xref:fundamentals/servers/kestrel). [Расширение mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) и связанные с ним модули создают обратный прокси-сервер.

## <a name="prerequisites"></a>Предварительные требования

* Сервер под управлением CentOS 7 и учетная запись обычного пользователя с правами sudo.
* Установите среду выполнения .NET Core на сервере.
   1. Перейдите на [страницу скачивания .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Выберите последнюю не предварительную версию .NET Core.
   1. Скачайте последнюю не предварительную версию среды выполнения из таблицы под заголовком **Run apps — Runtime** (Выполнение приложений — среда выполнения).
   1. Щелкните ссылку **Package manager instructions** (Инструкции диспетчера пакетов) для Linux и выполните инструкции для CentOS.
* Существующее приложение ASP.NET Core.

Перезапустить приложения ASP.NET Core, размещенные на сервере, можно в любой момент после обновления общей платформы.

## <a name="publish-and-copy-over-the-app"></a>Публикация и копирование приложения

Настройте приложение, чтобы [его развертывание зависело от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Если приложение запускается локально и не настроено для безопасного подключения (HTTPS), следует применять один из следующих подходов.

* Настройка приложения для обработки безопасных локальных подключений. Дополнительные сведения см. в разделе [Конфигурация HTTPS](#https-configuration).
* Удалите `https://localhost:5001` (при его наличии) из свойства `applicationUrl` в файле *Properties/launchSettings.json*.

Запустите [dotnet publish](/dotnet/core/tools/dotnet-publish) в среде разработки, чтобы упаковать приложение в каталог (например, *bin/Release/&lt;target_framework_moniker&gt;/publish*), который может выполняться на сервере:

```dotnetcli
dotnet publish --configuration Release
```

Приложение может быть опубликовано как [автономное развертывание](/dotnet/core/deploying/#self-contained-deployments-scd), если вы предпочитаете не сохранять среду выполнения .NET Core на сервере.

Скопируйте приложение ASP.NET Core на сервер с помощью инструмента, интегрированного в ваш рабочий процесс (например, SCP или SFTP). Обычно веб-приложения находятся в каталоге *var* (например, *var/www/helloapp*).

> [!NOTE]
> Если развертывание выполняется в рабочей среде, рабочий процесс непрерывной интеграции автоматически опубликует приложение и скопирует его ресурсы на сервер.

## <a name="configure-a-proxy-server"></a>Настройка прокси-сервера

Обратный прокси-сервер — это стандартный вариант настройки для обслуживания динамических веб-приложений. Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET.

Прокси-сервер перенаправляет запросы клиента на другой сервер, а не обрабатывает их самостоятельно. Обратный прокси-сервер перенаправляет запросы в фиксированное назначение обычно от имени клиентов. При работе с этим руководством мы настроим Apache в качестве обратного прокси-сервера, который работает на том же сервере, где Kestrel предоставляет приложение ASP.NET Core.

Так как запросы перенаправляются обратным прокси-сервером, используйте [ПО промежуточного слоя перенаправления заголовков](xref:host-and-deploy/proxy-load-balancer), которое входит в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/). Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.

Любой компонент, который зависит от схемы, например проверка подлинности, генерация ссылок, перенаправление и геолокация, должен находиться после вызова ПО промежуточного слоя перенаправления заголовков.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Вызовите <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> метод наверху `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя. В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Если параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не задан для ПО промежуточного слоя, по умолчанию перенаправляются заголовки `None`.

Прокси-серверы под управлением петлевых адресов (`127.0.0.0/8, [::1]`), включая стандартные адреса localhost (127.0.0.1), считаются доверенными по умолчанию. Если запросы между Интернетом и веб-сервером обрабатывают другие прокси-серверы или сети организации, добавьте их в список <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> или <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> с помощью <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. Следующий пример добавляет доверенный прокси-сервер с IP-адресом 10.0.0.100 в ПО промежуточного слоя для перенаправления заголовков `KnownProxies` в `Startup.ConfigureServices`:

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Установка Apache

Обновите пакеты CentOS до последних стабильных версий:

```bash
sudo yum update -y
```

Установите веб-сервер Apache на CentOS, выполнив одну команду `yum`:

```bash
sudo yum -y install httpd mod_ssl
```

Пример выходных данных этой команды:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> В нашем примере выходные данные содержат строку httpd.86_64, так как используется 64-разрядная версия CentOS 7. Чтобы проверить, где установлен Apache, выполните `whereis httpd` из командной строки.

### <a name="configure-apache"></a>Настройка Apache

Файлы конфигурации для Apache находятся в каталоге `/etc/httpd/conf.d/`. В алфавитном порядке обрабатываются все файлы с расширением *.conf*, а также файлы конфигурации модуля из папки `/etc/httpd/conf.modules.d/`, где содержатся файлы конфигурации, необходимые для загрузки модулей.

Создайте для приложения файл конфигурации с именем *helloapp.conf*:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

::: moniker range=">= aspnetcore-5.0"

Блок `VirtualHost` может встречаться несколько раз в одном или нескольких файлах на сервере. С представленным выше файлом конфигурации Apache принимает трафик от любого источника через порт 80. Он обслуживает домен `www.example.com`, а псевдоним `*.example.com` указывает на тот же веб-сайт. Дополнительные сведения см. в статье [о поддержке виртуальных узлов на основе имен](https://httpd.apache.org/docs/current/vhosts/name-based.html). Запросы к корневому каталогу перенаправляются на порт 5000 того же сервера по адресу 127.0.0.1. Для двусторонней связи требуются `ProxyPass` и `ProxyPassReverse`. Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel/endpoints).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Блок `VirtualHost` может встречаться несколько раз в одном или нескольких файлах на сервере. С представленным выше файлом конфигурации Apache принимает трафик от любого источника через порт 80. Он обслуживает домен `www.example.com`, а псевдоним `*.example.com` указывает на тот же веб-сайт. Дополнительные сведения см. в статье [о поддержке виртуальных узлов на основе имен](https://httpd.apache.org/docs/current/vhosts/name-based.html). Запросы к корневому каталогу перенаправляются на порт 5000 того же сервера по адресу 127.0.0.1. Для двусторонней связи требуются `ProxyPass` и `ProxyPassReverse`. Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).

::: moniker-end

> [!WARNING]
> Если не удастся указать правильную [директиву ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) в блоке **VirtualHost**, приложение будет иметь значительные уязвимости. Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.example.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

Ведение журнала можно настроить отдельно для каждого `VirtualHost` с помощью директив `ErrorLog` и `CustomLog`. Журналы ошибок сохраняются в расположение `ErrorLog`, а параметр `CustomLog` задает имя и формат для файла журнала. В нашем примере здесь фиксируются сведения о запросах. Для каждого запроса создается одна строка.

Сохраните файл и протестируйте конфигурацию. Если проверка выполнена успешно, ответ должен быть `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Перезапустите Apache.

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Мониторинг приложения

Теперь Apache настроен на перенаправление запросов к `http://localhost:80` в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`. Но Apache не настроен для управления процессом Kestrel. Для запуска и мониторинга базового веб-приложения используйте *systemd* и создайте файл службы. *systemd* — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими.

### <a name="create-the-service-file"></a>Создание файла службы

Создайте файл определения службы.

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Пример файла службы для приложения:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

В предыдущем примере управляющий службой пользователь задается с помощью параметра `User`. Этот пользователь (`apache`) должен существовать и иметь права владельца в отношении файлов приложения.

Используйте `TimeoutStopSec`, чтобы настроить время ожидания до завершения работы приложения после начального сигнала прерывания. Если приложение не завершит работу в течение этого периода, оно прерывается сигналом SIGKILL. Укажите значение в секундах без единиц измерения (например, `150`), значение интервала (например, `2min 30s`) или значение `infinity`, которое отключает время ожидания. По умолчанию `TimeoutStopSec` принимает значение `DefaultTimeoutStopSec` в файле конфигурации диспетчера (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). В большинстве дистрибутивов по умолчанию устанавливается время ожидания 90 секунд.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Некоторые значения (например, строки подключения SQL) необходимо экранировать, чтобы поставщики конфигурации могли считать переменные среды. Используйте следующую команду, чтобы создать правильно экранированное значение для файла конфигурации:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды. Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия. [Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации. В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды. Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия. [Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации. В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Сохраните файл и включите службу.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Запустите службу и убедитесь, что она работает.

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Теперь, когда обратный прокси-сервер настроен и *systemd* управляет процессом Kestrel, веб-приложение можно считать полностью настроенным и вы можете обратиться к нему по адресу `http://localhost` из браузера на локальном компьютере. Заголовок **Server** в ответе подтверждает, что приложение ASP.NET Core обслуживается Kestrel.

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Просмотр журналов

Так как веб-приложением, использующим Kestrel, управляет *systemd*, все события и процессы регистрируются в централизованном журнале. Но этот журнал содержит все записи обо всех службах и процессах, управляемых *systemd*. Чтобы просмотреть элементы, связанные с `kestrel-helloapp.service`, используйте следующую команду.

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Чтобы отфильтровать элементы по времени, укажите в команде параметры времени. Например, `--since today` позволяет отфильтровать данные за текущий день, а `--until 1 hour ago` — просмотреть записи за предыдущий час. Дополнительные сведения см. на странице руководства о команде [journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Защита данных

[Стек защиты данных в ASP.NET Core](xref:security/data-protection/introduction) используется определенным [ПО промежуточного слоя](xref:fundamentals/middleware/index) ASP.NET Core, включая промежуточное ПО для проверки подлинности (например, промежуточное ПО файлов cookie) и средствами защиты от подделки межсайтовых запросов. Даже если API-интерфейсы защиты данных не вызываются из пользовательского кода, необходимо настроить защиту данных для создания постоянного [хранилища криптографических ключей](xref:security/data-protection/implementation/key-management). Если защита данных не настроена, ключи хранятся в памяти и удаляются при перезапуске приложения.

Если набор ключей хранится в памяти, при перезапуске приложения происходит следующее:

* Все токены аутентификации, использующие файлы cookie, становятся недействительными.
* При выполнении следующего запроса пользователю требуется выполнить вход снова.
* Все данные, защищенные с помощью набора ключей, больше не могут быть расшифрованы. Это могут быть [токены CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) и [файлы cookie временных данных ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).

Сведения о настройке защиты данных для хранения и шифрования набора ключей см. в приведенных ниже статьях.

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Защита приложения

### <a name="configure-firewall"></a>Настройка брандмауэра

*Firewalld* — это динамическая управляющая программа брандмауэра, которая поддерживает зоны сети. Фильтрацию портов и пакетов можно по-прежнему осуществлять через iptables. *Firewalld* обычно устанавливается в системе по умолчанию. `yum` позволяет установить этот пакет или убедиться, что он установлен.

```bash
sudo yum install firewalld -y
```

С помощью `firewalld` вы можете открыть только те порты, которые необходимые для работы приложения. В этом случае используются порты 80 и 443. Следующие команды назначают порты 80 и 443 постоянно открытыми.

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Обновите параметры брандмауэра. Проверьте, что доступные службы и порты находятся в зоне по умолчанию. Эти параметры можно просмотреть с помощью `firewall-cmd -h`.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>Конфигурация HTTPS

**Настройка приложения для безопасных (HTTPS) локальных подключений**

Команда [dotnet run](/dotnet/core/tools/dotnet-run) использует файл приложения *Properties/launchSettings.json*, который настраивает приложение для прослушивания URL-адресов, заданных свойством `applicationUrl` (например, `https://localhost:5001;http://localhost:5000`).

Настройте приложение для использования при разработке сертификата для команды `dotnet run` или среды разработки (F5 или CTRL + F5 в Visual Studio Code), используя один из следующих подходов.

::: moniker range=">= aspnetcore-5.0"

* [Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel/endpoints#configuration) (*рекомендуется*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* [Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel#configuration) (*рекомендуется*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

**Настройка обратного прокси-сервера для безопасного подключения клиентов (HTTPS)**

Apache для HTTPS настраивается с помощью модуля *mod_ssl*. При установке модуля *httpd* автоматически добавляется и модуль *mod_ssl*. Если он по каким-то причинам отсутствует, добавьте его в систему с помощью `yum`.

```bash
sudo yum install mod_ssl
```

Чтобы принудительно использовать HTTPS, установите модуль `mod_rewrite` для перезаписи URL-адресов:

```bash
sudo yum install mod_rewrite
```

Измените файл *helloapp.conf*, чтобы разрешить перезапись URL-адресов и безопасный обмен данными через порт 443:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> В этом примере используется локально созданный сертификат. В **SSLCertificateFile** должен быть указан основной файл сертификата для доменного имени. В **SSLCertificateKeyFile** должен быть указан файл ключа, сформированный при создании CSR. В **SSLCertificateChainFile** должен быть указан файл промежуточного сертификата (если он существует), предоставленный центром сертификации.

Сохраните файл и протестируйте конфигурацию.

```bash
sudo service httpd configtest
```

Перезапустите Apache.

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Дополнительные предложения Apache

### <a name="restart-apps-with-shared-framework-updates"></a>Перезапуск приложений после обновления общей платформы

После обновления общей платформы на сервере перезапустите размещенные на нем приложения ASP.NET Core.

### <a name="additional-headers"></a>Дополнительные заголовки

Для защиты от вредоносных атак следует изменить или добавить несколько заголовков. Убедитесь, что модуль `mod_headers` установлен.

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Защита Apache от атак кликджекинга

[Кликджекинг](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger) (или *атака с подменой пользовательского интерфейса*) является вредоносной атакой, при которой посетителя сайта обманным путем вынуждают щелкнуть ссылку или нажать кнопку не той страницы, на которой он находится. Используйте `X-FRAME-OPTIONS` для защиты сайта.

Чтобы уменьшить риск атак кликджекинга, выполните указанные ниже действия.

1. Измените файл *httpd.conf*.

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Добавьте строку `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.
1. Сохраните файл.
1. Перезапустите Apache.

#### <a name="mime-type-sniffing"></a>Сканирование типа MIME

Заголовок `X-Content-Type-Options` защищает Internet Explorer от *сканирования MIME* (определения `Content-Type` для файла по его содержимому). Если сервер задает заголовок `Content-Type` со значением `text/html`, и при этом установлен параметр `nosniff`, Internet Explorer отображает содержимое как `text/html` независимо от содержимого файла.

Измените файл *httpd.conf*.

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Добавьте строку `Header set X-Content-Type-Options "nosniff"`. Сохраните файл. Перезапустите Apache.

### <a name="load-balancing"></a>Балансировка нагрузки

В этом примере показано, как установить и настроить Apache в CentOS 7 и Kestrel на том же компьютере. Чтобы устранить единую точку отказа, воспользуйтесь *mod_proxy_balancer* и измените значение **VirtualHost** для управления несколькими экземплярами веб-приложений за прокси-сервером Apache.

```bash
sudo yum install mod_proxy_balancer
```

В представленном ниже файле конфигурации настроен дополнительный экземпляр `helloapp`, работающий на порте 5001. В разделе *Proxy* настраивается конфигурация подсистемы балансировки нагрузки с двумя членами для распределения нагрузки методом *byrequests*.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Ограничения скорости

С помощью элемента *mod_ratelimit*, который входит в модуль *httpd*, можно ограничить пропускную способность для клиентов:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

В примере файла пропускная способность составляет 600 Кбит/с в корневой папке.

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Длинные поля заголовка запроса

Параметры прокси-сервера по умолчанию обычно ограничивают длину полей заголовка запроса значением 8190 байт. Приложению могут потребоваться более длинные поля (например, приложениям, использующим [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). В этом случае требуется скорректировать директиву [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) для прокси-сервера. Применяемое значение зависит от конкретного сценария. Дополнительные сведения см. в документации сервера.

> [!WARNING]
> Не увеличивайте значение `LimitRequestFieldSize` по умолчанию, если это не требуется. Увеличение значения повышает риск переполнения буфера и атак типа "отказ в обслуживании" (DoS) со стороны злоумышленников.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Необходимые компоненты для .NET Core в Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
