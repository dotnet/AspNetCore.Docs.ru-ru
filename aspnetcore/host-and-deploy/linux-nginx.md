---
title: Среда размещения ASP.NET Core в операционной системе Linux с Nginx
author: rick-anderson
description: В статье описывается процедура настройки Nginx как обратного прокси-сервера на Ubuntu 16.04 для перенаправления трафика HTTP в веб-приложение ASP.NET Core, выполняемое в Kestrel.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 6a8fd8e3498dda9b7c10834791e64df6276e2823
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253024"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Среда размещения ASP.NET Core в операционной системе Linux с Nginx

Автор [Сурабх Ширхатти](https://twitter.com/sshirhatti)

В этом руководстве описывается настройка готовой к работе среды ASP.NET Core на сервере Ubuntu 16.04. Эти инструкции могут подходить для более поздних версий Ubuntu, но они еще не были протестированы в этих версиях.

Сведения о других дистрибутивах Linux, поддерживаемых платформой ASP.NET Core, см. в разделе о [необходимых компонентах для .NET Core в Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Для Ubuntu 14.04 в качестве решения для мониторинга процесса Kestrel рекомендуется `supervisord`. Решение `systemd` в Ubuntu 14.04 недоступно. Инструкции для Ubuntu 14.04 см. в [предыдущей версии этого раздела](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

В этом руководстве рассматривается

* размещение существующего приложения ASP.NET Core за обратным прокси-сервером;
* настройка обратного прокси-сервера для перенаправления запросов на веб-сервер Kestrel;
* проверка выполнения веб-приложения как управляющей программы при запуске системы;
* настройка средства управления процессами, с помощью которого можно перезапустить веб-приложение.

## <a name="prerequisites"></a>Предварительные требования

1. Доступ к серверу Ubuntu 16.04 под учетной записью обычного пользователя с правами sudo.
1. Установите среду выполнения .NET Core на сервере.
   1. Перейдите на [страницу скачивания .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
   1. Выберите последнюю не предварительную версию .NET Core.
   1. Скачайте последнюю не предварительную версию среды выполнения из таблицы под заголовком **Run apps — Runtime** (Выполнение приложений — среда выполнения).
   1. Щелкните ссылку **Package manager instructions** (Инструкции диспетчера пакетов) для Linux и выполните инструкции для соответствующей версии Ubuntu.
1. Существующее приложение ASP.NET Core.

Перезапустить приложения ASP.NET Core, размещенные на сервере, можно в любой момент после обновления общей платформы.

## <a name="publish-and-copy-over-the-app"></a>Публикация и копирование приложения

Настройте приложение, чтобы [его развертывание зависело от платформы](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Если приложение запускается локально и не настроено для безопасного подключения (HTTPS), следует применять один из следующих подходов.

* Настройка приложения для обработки безопасных локальных подключений. Дополнительные сведения см. в разделе [Конфигурация HTTPS](#https-configuration).
* Удалите `https://localhost:5001` (при его наличии) из свойства `applicationUrl` в файле `Properties/launchSettings.json`.

Выполните команду [dotnet publish](/dotnet/core/tools/dotnet-publish) в среде разработки, чтобы упаковать приложение в каталог (например, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, где заполнитель `{TARGET FRAMEWORK MONIKER}` является моникером целевой платформы или TFM), который может выполняться на сервере:

```dotnetcli
dotnet publish --configuration Release
```

Приложение может быть опубликовано как [автономное развертывание](/dotnet/core/deploying/#self-contained-deployments-scd), если вы предпочитаете не сохранять среду выполнения .NET Core на сервере.

Скопируйте приложение ASP.NET Core на сервер с помощью инструмента, интегрированного в ваш рабочий процесс (например, `SCP`, `SFTP`). Обычно веб-приложения находятся в каталоге `var` (например, `var/www/helloapp`).

> [!NOTE]
> Если развертывание выполняется в рабочей среде, рабочий процесс непрерывной интеграции автоматически опубликует приложение и скопирует его ресурсы на сервер.

Проверьте работу приложения:

1. Запустите приложение в командной строке: `dotnet <app_assembly>.dll`.
1. В браузере откройте страницу `http://<serveraddress>:<port>`, чтобы убедиться, что приложение локально работает на платформе Linux.

## <a name="configure-a-reverse-proxy-server"></a>Настройка обратного прокси-сервера

Обратный прокси-сервер — это стандартный вариант настройки для обслуживания динамических веб-приложений. Обратный прокси-сервер завершает HTTP-запрос и перенаправляет его в приложение ASP.NET Core.

### <a name="use-a-reverse-proxy-server"></a>Использование обратного прокси-сервера

Kestrel является отличным решением для обслуживания динамического содержимого из ASP.NET Core. При этом компоненты для работы с веб-службами не настолько функциональны, как серверы типа IIS, Apache или Nginx. Обратный прокси-сервер может облегчить такую работу, как обслуживание статического содержимого, кэширование и сжатие запросов, а также терминирование HTTPS с HTTP-сервера. Обратный прокси-сервер можно разместить на отдельном компьютере или развернуть параллельно с HTTP-сервером.

В контексте данного руководства используется отдельный экземпляр Nginx. Он выполняется на том же сервере, что и HTTP-сервер. Настройки можно выбирать в зависимости от требований.

Так как запросы перенаправляются обратным прокси-сервером, используйте [ПО промежуточного слоя перенаправленных заголовков](xref:host-and-deploy/proxy-load-balancer) из пакета [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides). Это ПО обновляет `Request.Scheme`, используя заголовок `X-Forwarded-Proto`, что обеспечивает правильную работу URI перенаправления и других политик безопасности.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Вызовите <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> метод наверху `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя. В ПО промежуточного слоя настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto`:

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Если параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не задан для ПО промежуточного слоя, по умолчанию перенаправляются заголовки `None`.

Прокси-серверы под управлением адресов замыкания на себя (`127.0.0.0/8`, `[::1]`), включая стандартные адреса localhost (`127.0.0.1`), считаются доверенными по умолчанию. Если запросы между Интернетом и веб-сервером обрабатывают другие прокси-серверы или сети организации, добавьте их в список <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> или <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> с помощью <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>. Следующий пример добавляет доверенный прокси-сервер с IP-адресом 10.0.0.100 в ПО промежуточного слоя для перенаправления заголовков `KnownProxies` в `Startup.ConfigureServices`:

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Для получения дополнительной информации см. <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Установка Nginx

Установите Nginx с помощью команды `apt-get`. Программа установки создает сценарий инициализации `systemd`, который запускает Nginx как управляющую программу при запуске системы. Следуйте инструкциям по установке для Ubuntu в разделе [Nginx: официальные пакеты Debian и Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Если необходимы дополнительные модули Nginx, может потребоваться создание Nginx из источника.

Так как Nginx устанавливается впервые, запустите его напрямую, выполнив следующую команду.

```bash
sudo service nginx start
```

В браузере должна открыться стартовая страница Nginx по умолчанию. Целевая страница доступна по адресу `http://<server_IP_address>/index.nginx-debian.html`.

### <a name="configure-nginx"></a>Настройка Nginx

Чтобы настроить Nginx как обратный прокси-сервер для перенаправления HTTP-запросов в ваше приложение ASP.NET Core, измените файл `/etc/nginx/sites-available/default`. Откройте этот файл в текстовом редакторе и замените его содержимое на следующий фрагмент кода:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Если вы работаете с приложением SignalR или Blazor Server, дополнительные сведения см. в разделах о <xref:signalr/scale#linux-with-nginx> и <xref:blazor/host-and-deploy/server#linux-with-nginx> соответственно.

Если отсутствуют совпадения для `server_name`, Nginx использует сервер по умолчанию. Если сервер по умолчанию не определен, первый сервер в файле конфигурации является сервером по умолчанию. Рекомендуется добавить в качестве сервера по умолчанию определенный сервер, который возвращает код состояния 444 в файле конфигурации. Ниже приведен пример конфигурации сервера по умолчанию:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

С представленным выше файлом конфигурации и сервером по умолчанию Nginx принимает трафик от любого источника через порт 80 с заголовком узла `example.com` или `*.example.com`. Запросы, не соответствующие этим узлам, не будут перенаправляться в Kestrel. Запросы, которые им соответствуют, Nginx перенаправляет в Kestrel по адресу `http://localhost:5000`. Дополнительные сведения см. в статье [Как nginx обрабатывает запросы](https://nginx.org/docs/http/request_processing.html). Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel/endpoints).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

С представленным выше файлом конфигурации и сервером по умолчанию Nginx принимает трафик от любого источника через порт 80 с заголовком узла `example.com` или `*.example.com`. Запросы, не соответствующие этим узлам, не будут перенаправляться в Kestrel. Запросы, которые им соответствуют, Nginx перенаправляет в Kestrel по адресу `http://localhost:5000`. Дополнительные сведения см. в статье [Как nginx обрабатывает запросы](https://nginx.org/docs/http/request_processing.html). Сведения о том, как изменить IP-адрес/порт Kestrel, см. в разделе [Kestrel: конфигурация конечной точки](xref:fundamentals/servers/kestrel#endpoint-configuration).

::: moniker-end

> [!WARNING]
> Если не будет указана правильная [директива server_name](https://nginx.org/docs/http/server_names.html), приложение будет подвержено значительным уязвимостям. Привязки с подстановочными знаками на уровне дочерних доменов (например, `*.example.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

Установив конфигурацию Nginx, выполните команду `sudo nginx -t`, чтобы проверить синтаксис файлов конфигурации. Если проверка файла конфигурации прошла успешно, заставьте Nginx принять изменения, выполнив команду `sudo nginx -s reload`.

Для непосредственного запуска приложений на сервере:

1. Перейдите в каталог приложения.
1. Запустите приложение: `dotnet <app_assembly.dll>`, где `app_assembly.dll` — имя файла сборки приложения.

Если приложение выполняется на сервере, но не отвечает по Интернету, проверьте брандмауэр сервера и убедитесь, что порт 80 открыт. При использовании виртуальной машины Ubuntu Azure добавьте правило группы безопасности сети (NSG), которое разрешает входящий трафик через порт 80. Не нужно включать правило исходящего трафика на порте 80, так как исходящий трафик предоставляется автоматически при включении правила для входящего трафика.

Когда закончите тестировать приложение, завершите его работу с помощью клавиш <kbd>CTRL</kbd> + <kbd>C</kbd> в командной строке.

## <a name="monitor-the-app"></a>Мониторинг приложения

Сервер настроен на перенаправление запросов к `http://<serveraddress>:80` в приложение ASP.NET Core, выполняемое в Kestrel по адресу `http://127.0.0.1:5000`. При этом Nginx не настроен для управления процессом Kestrel. Для запуска и мониторинга соответствующего веб-приложения можно использовать `systemd` и создать файл службы. `systemd` — это система инициализации, предоставляющая различные функции для запуска и остановки процессов, а также управления ими. 

### <a name="create-the-service-file"></a>Создание файла службы

Создайте файл определения службы.

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Далее представлен пример файла службы для нашего приложения:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

В предыдущем примере управляющий службой пользователь задается с помощью параметра `User`. Этот пользователь (`www-data`) должен существовать и иметь права владельца в отношении файлов приложения.

Используйте `TimeoutStopSec`, чтобы настроить время ожидания до завершения работы приложения после начального сигнала прерывания. Если приложение не завершит работу в течение этого периода, оно прерывается сигналом SIGKILL. Укажите значение в секундах без единиц измерения (например, `150`), значение интервала (например, `2min 30s`) или значение `infinity`, которое отключает время ожидания. `TimeoutStopSec` по умолчанию использует значение `DefaultTimeoutStopSec` в файле конфигурации диспетчера (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`). В большинстве дистрибутивов по умолчанию устанавливается время ожидания 90 секунд.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux имеет файловую систему, в которой учитывается регистр символов. Задание `ASPNETCORE_ENVIRONMENT` для `Production` приведет к поиску файла конфигурации `appsettings.Production.json`, а не `appsettings.production.json`.

Некоторые значения (например, строки подключения SQL) необходимо экранировать, чтобы поставщики конфигурации могли читать переменные среды. Используйте следующую команду, чтобы создать правильно экранированное значение для файла конфигурации:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды. Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия. [Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации. В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Разделители-двоеточия (`:`) не поддерживаются в именах переменных среды. Следует использовать двойной знак подчеркивания (`__`) вместо двоеточия. [Поставщик конфигурации переменных среды](xref:fundamentals/configuration/index#environment-variables-configuration-provider) преобразует двойные символы подчеркивания в двоеточия, когда переменные среды считываются в конфигурации. В следующем примере ключ строки подключения `ConnectionStrings:DefaultConnection` задается в файле определения службы как `ConnectionStrings__DefaultConnection`.

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Сохраните файл и включите службу.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Запустите службу и убедитесь, что она работает.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Теперь, когда обратный прокси-сервер настроен и `systemd`управляет процессом Kestrel, веб-приложение можно считать полностью настроенным и вы можете обратиться к нему по адресу`http://localhost` из браузера на локальном компьютере. Оно также доступно для удаленных компьютеров, несмотря на наличие блокирующих трафик межсетевых экранов. Заголовок `Server` в ответе подтверждает, что приложение ASP.NET Core обслуживается Kestrel.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Просмотр журналов

Так как веб-приложение, использующее Kestrel, управляется через `systemd`, все события и процессы регистрируются в централизованном журнале. При этом журнал содержит все записи обо всех службах и процессах, управляемых `systemd`. Чтобы просмотреть элементы, связанные с `kestrel-helloapp.service`, используйте следующую команду.

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Кроме того, количество возвращаемых записей можно уменьшить, указав параметры времени, например `--since today`, `--until 1 hour ago` или их комбинацию.

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

## <a name="long-request-header-fields"></a>Длинные поля заголовка запроса

Параметры прокси-сервера по умолчанию обычно ограничивают длину полей заголовка запроса значением 4 КБ или 8 КБ (в зависимости от платформы). Приложению могут потребоваться более длинные поля (например, приложениям, использующим [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)). В этом случае требуется скорректировать параметры по умолчанию для прокси-сервера. Применяемые значения зависят от конкретного сценария. Дополнительные сведения см. в документации сервера.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Не увеличивайте значение буферов прокси-сервера по умолчанию, если это не требуется. Увеличение этих значений повышает риск переполнения буфера и атак типа "отказ в обслуживании" (DoS) со стороны злоумышленников.

## <a name="secure-the-app"></a>Защита приложения

### <a name="enable-apparmor"></a>Включение AppArmor

Начиная с версии 2.6 ядро Linux включает платформу модулей безопасности Linux (LSM). LSM поддерживают различные реализации модулей безопасности. [AppArmor](https://wiki.ubuntu.com/AppArmor) — это LSM, который реализует систему обязательного контроля доступа, позволяющую ограничивать программу определенным набором ресурсов. Убедитесь, что AppArmor включен и правильно настроен.

### <a name="configure-the-firewall"></a>Настройка брандмауэра

Закройте все внешние порты, которые не используются. Простой брандмауэр (ufw) позволяет взаимодействовать с `iptables`. Для настройки брандмауэра предоставляется CLI.

> [!WARNING]
> Неправильно настроенный брандмауэр предотвратит доступ ко всей системе. Если задать неправильный порт SSH, то при использовании SSH для подключения к системе произойдет ее блокировка. Порт по умолчанию — 22. Дополнительные сведения см. в [вводной статье по ufw](https://help.ubuntu.com/community/UFW) и в [этом руководстве](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Установите `ufw` и настройте его для разрешения прохождения трафика через все необходимые порты.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Защита Nginx

#### <a name="change-the-nginx-response-name"></a>Изменение имени ответа Nginx

Отредактируйте файл `src/http/ngx_http_header_filter_module.c`:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Настройка параметров

Настройте дополнительные обязательные модули на сервере. Для дополнительной защиты приложения можно использовать межсетевой экран для веб-приложений, например [ModSecurity](https://www.modsecurity.org/).

#### <a name="https-configuration"></a>Конфигурация HTTPS

**Настройка приложения для безопасных (HTTPS) локальных подключений**

Команда [dotnet run](/dotnet/core/tools/dotnet-run) использует файл приложения *Properties/launchSettings.json*, который настраивает приложение для прослушивания URL-адресов, заданных свойством `applicationUrl`. Например, `https://localhost:5001;http://localhost:5000`.

Настройте приложение, чтобы оно использовало при разработке сертификат для команды `dotnet run` или среды разработки (<kbd>F5</kbd> или <kbd>CTRL</kbd>+<kbd>F5</kbd> в Visual Studio Code), используя один из следующих подходов:

::: moniker range=">= aspnetcore-5.0"

* [Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel/endpoints#configuration) (*рекомендуется*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* [Замена сертификата по умолчанию из конфигурации](xref:fundamentals/servers/kestrel#configuration) (*рекомендуется*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

**Настройка обратного прокси-сервера для безопасного подключения клиентов (HTTPS)**

* Настройте сервер для прослушивания трафика HTTPS через порт 443, указав действительный сертификат, выпущенный доверенным центром сертификации (ЦС).

* Обеспечьте дополнительную защиту, применив некоторые методы, показанные в представленном ниже файле */etc/nginx/nginx.conf*. Это может быть выбор более строгого шифра и перенаправление всего HTTP-трафика в HTTPS.

  > [!NOTE]
  > Для среды разработки рекомендуется использовать временные (302), а не постоянные перенаправления (301). Кэширование ссылок может привести к нестабильной работе в средах разработки.

* При добавлении заголовка `HTTP Strict-Transport-Security` (HSTS) все последующие запросы клиента будут проходить по протоколу HTTPS.

  Чтобы узнать важные рекомендации по HSTS, см. <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.

* Если протокол HTTPS будет отключен в будущем, используйте один из следующих подходов.

  * Не добавляйте заголовок HSTS.
  * Выберите короткое значение `max-age`.

Добавьте файл конфигурации */etc/nginx/proxy.conf*:

[!code-nginx[](linux-nginx/proxy.conf)]

**Замените** содержимое файла конфигурации */etc/nginx/nginx.conf* следующим файлом. В этом примере показаны разделы `http` и `server` одного и того же файла конфигурации.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> Приложениям Blazor WebAssembly требуется большее значение параметра `burst` с учетом большего количества запросов, выполняемых приложением. Для получения дополнительной информации см. <xref:blazor/host-and-deploy/webassembly#nginx>.

#### <a name="secure-nginx-from-clickjacking"></a>Защита Nginx от кликджекинга

[Кликджекинг](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger) (или *атака с подменой пользовательского интерфейса*) является вредоносной атакой, при которой посетителя сайта обманным путем вынуждают щелкнуть ссылку или нажать кнопку не той страницы, на которой он находится. Используйте `X-FRAME-OPTIONS` для защиты сайта.

Чтобы уменьшить риск атак кликджекинга, выполните указанные ниже действия.

1. Измените файл *nginx.conf*.

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Добавьте строку: `add_header X-Frame-Options "SAMEORIGIN";`

1. Сохраните файл.
1. Перезапустите Nginx.

#### <a name="mime-type-sniffing"></a>Сканирование типа MIME

Этот заголовок предотвращает MIME-сканирование ответов с указанным типом содержимого в большинстве браузеров, запрещая браузеру переопределять тип содержимого ответа. Параметр `nosniff` означает, что если сервер определяет содержимое как `text/html`, то браузер будет обрабатывать его как `text/html`.

1. Измените файл *nginx.conf*.

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Добавьте строку: `add_header X-Content-Type-Options "nosniff";`

1. Сохраните файл.
1. Перезапустите Nginx.

## <a name="additional-nginx-suggestions"></a>Дополнительные предложения Nginx

После обновления общей платформы на сервере перезапустите размещенные на нем приложения ASP.NET Core.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Необходимые компоненты для .NET Core в Linux](/dotnet/core/linux-prerequisites)
* [Nginx: двоичные выпуски: официальные пакеты Debian и Ubuntu](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: использование перенаправленного заголовка](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
