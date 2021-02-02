---
title: Реализации веб-сервера в ASP.NET Core
author: rick-anderson
description: Откройте возможности веб-серверов Kestrel и HTTP.sys для ASP.NET Core. Рекомендации по выбору сервера и сведения о сценариях использования обратного прокси-сервера.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217561"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Реализации веб-сервера в ASP.NET Core

Авторы: [Том Дисктра](https://github.com/tdykstra) (Tom Dykstra), [Стив Смит](https://ardalis.com/) (Steve Smith), [Стивен Хальтер](https://twitter.com/halter73) (Stephen Halter) и [Крис Росс](https://github.com/Tratcher) (Chris Ross)

Приложение ASP.NET Core выполняется вместе с внутрипроцессной реализацией HTTP-сервера. Реализация сервера прослушивает HTTP-запросы и передает их в приложение как набор [функций запросов](xref:fundamentals/request-features), объединенных в <xref:Microsoft.AspNetCore.Http.HttpContext>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

В состав ASP.NET Core входит следующее:

* Сервер [Kestrel](xref:fundamentals/servers/kestrel) — это реализация кроссплатформенного HTTP-сервера по умолчанию. Kestrel обеспечивает максимальную производительность и использование памяти, однако в ней нет некоторых дополнительных функций в HTTP.sys. Дополнительные сведения см. в разделе [Сравнение Kestrel и HTTP.sys](#korh) в этом документе.
* HTTP-сервер IIS — это [внутрипроцессный сервер для службы IIS](#hosting-models).
* [Сервер HTTP.sys](xref:fundamentals/servers/httpsys) — это HTTP-сервер, предназначенный только для Windows и основанный на [драйвере ядра HTTP.sys и API HTTP-сервера](/windows/desktop/Http/http-api-start-page).

При использовании [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) или [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) приложение запускается одним из следующих способов:

* В том же процессе, что и рабочий процесс IIS ([модель внутрипроцессного размещения](#hosting-models)), с использованием HTTP-сервера IIS. *Внутрипроцессное размещение* является рекомендуемой конфигурацией.
* В процессе отдельно от рабочего процесса IIS ([модель внепроцессного размещения](#hosting-models)) с использованием [сервера Kestrel](#kestrel).

[Модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) — это собственный модуль IIS, который обрабатывает собственные запросы IIS между службами IIS и HTTP-сервером IIS (внутрипроцессно) или сервером Kestrel. Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module>.

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a>Сравнение Kestrel и HTTP.sys

Kestrel имеет следующие преимущества по сравнению с HTTP.sys:

  * Повышенная производительность и более эффективное использование памяти.
  * Кроссплатформенные
  * Гибкость, поскольку разработка и установка исправлений осуществляется независимо от операционной системы.
  * Программный порт и конфигурация TLS.
  * Расширяемость, обеспечивающая использование таких протоколов, как [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs), и альтернативные транспорты.

HTTP.sys выступает в качестве общего компонента режима ядра со следующими функциями, которыми не обладает Kestrel:

  * Совместное использование портов
  * Проверка подлинности Windows в режиме ядра. [Kestrel поддерживает проверку подлинности только в режиме пользователя](xref:security/authentication/windowsauth#kestrel).
  * Быстрое проксирование с помощью передачи очередей
  * Прямая передача файлов
  * Кэширование откликов

## <a name="hosting-models"></a>Модели размещения

При внутрипроцессном размещении приложение ASP.NET Core выполняется в том же процессе, что и рабочий процесс IIS. При этом повышается производительность по сравнению с внепроцессным размещением, так как запросы не передаются через адаптер замыкания на себя (сетевой интерфейс, который возвращает исходящий сетевой трафик на тот же компьютер). IIS обрабатывает управление процессом с помощью [службы активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

При внепроцессном размещении приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, а модуль управляет процессами. Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы. Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Дополнительные сведения и инструкции по настройке см. в следующих статьях:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

 Сервер [Kestrel](xref:fundamentals/servers/kestrel) — это реализация кроссплатформенного HTTP-сервера по умолчанию. Kestrel обеспечивает максимальную производительность и использование памяти, однако в ней нет некоторых дополнительных функций в HTTP.sys. Дополнительные сведения см. в разделе [Сравнение Kestrel и HTTP.sys](#korh) в этом документе.

Kestrel используется в следующих сценариях:

* Сам по себе как пограничный сервер обработки запросов непосредственно из сети, включая Интернет.

  ![Kestrel взаимодействует с Интернетом напрямую, без обратного прокси-сервера](kestrel/_static/kestrel-to-internet2.png)

* С *обратным прокси-сервером*, таким как [службы IIS](https://www.iis.net/), [Nginx](https://nginx.org) или [Apache](https://httpd.apache.org/). Обратный прокси-сервер получает HTTP-запросы из Интернета и пересылает их в Kestrel.

  ![Kestrel взаимодействует с Интернетом косвенно, через обратный прокси-сервер, такой как IIS, Nginx или Apache.](kestrel/_static/kestrel-to-internet.png)

Поддерживается любая из этих конфигураций размещения &mdash; с обратным прокси-сервером и без него.

Инструкции по настройке Kestrel и сведения о том, когда следует использовать Kestrel в конфигурации обратного прокси-сервера, см. в статье <xref:fundamentals/servers/kestrel>.

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

В состав ASP.NET Core входит следующее:

* Сервер [Kestrel](xref:fundamentals/servers/kestrel) — кроссплатформенный HTTP-сервер по умолчанию.
* [Сервер HTTP.sys](xref:fundamentals/servers/httpsys) — это HTTP-сервер, предназначенный только для Windows и основанный на [драйвере ядра HTTP.sys и API HTTP-сервера](/windows/desktop/Http/http-api-start-page).

При использовании [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) или [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) приложение запускается отдельно от рабочего процесса IIS (*внепроцессно*) с [сервером Kestrel](#kestrel).

Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль обрабатывает управление процессами. Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы. Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.

![Модуль ASP.NET Core](_static/ancm-outofprocess.png)

Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра. Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS). Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.

Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`. Выполняются дополнительные проверки, и запросы не из модуля отклоняются. Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.

После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core. Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения. ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel. Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.

Инструкции по настройке модуля ASP.NET Core и IIS см. в следующих статьях:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core поставляется с [сервером Kestrel](xref:fundamentals/servers/kestrel), который является кроссплатформенным HTTP-сервером по умолчанию.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx с Kestrel

Сведения о том, как использовать Nginx в Linux в качестве обратного прокси-сервера для Kestrel, см. в <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache с Kestrel

Сведения о том, как использовать Apache в Linux в качестве обратного прокси-сервера для Kestrel, см. в <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

Если приложение ASP.NET Core запускается в Windows, вместо Kestrel можно использовать HTTP.sys. Kestrel рекомендуется использовать вместо HTTP.sys, если приложению не требуются функции, недоступные в Kestrel. Для получения дополнительной информации см. <xref:fundamentals/servers/httpsys>.

![HTTP.sys взаимодействует с Интернетом напрямую](httpsys/_static/httpsys-to-internet.png)

HTTP.sys можно также использовать для приложений, которые имеют доступ только к внутренней сети.

![HTTP.sys взаимодействует с внутренней сетью напрямую](httpsys/_static/httpsys-to-internal.png)

Инструкции по настройке HTTP.sys см. в статье <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>Инфраструктура сервера ASP.NET Core

<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>, доступный в методе `Startup.Configure`, предоставляет свойство <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> типа <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. Kestrel и HTTP.sys предоставляют только один компонент, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, но разные реализации сервера могут предоставлять дополнительные возможности.

`IServerAddressesFeature` можно использовать для того, чтобы узнать, какой порт в реализации сервера привязан к среде выполнения.

## <a name="custom-servers"></a>Пользовательские серверы

Если встроенные серверы не отвечают требованиям приложения, можно создать реализацию пользовательского сервера. В [руководстве по открытому веб-интерфейсу .NET (OWIN)](xref:fundamentals/owin) демонстрируется запись реализации <xref:Microsoft.AspNetCore.Hosting.Server.IServer> на основе [Nowin](https://github.com/Bobris/Nowin). Требуют реализации только интерфейсы компонентов, используемых приложением, но как минимум должны поддерживаться <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> и <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>.

## <a name="server-startup"></a>Запуск сервера

Сервер запускается, когда интегрированная среда разработки (IDE) или редактор запускает приложение:

* [Visual Studio](https://visualstudio.microsoft.com). Вы можете использовать профили запуска для запуска приложения и сервера с помощью [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) или консоли.
* [Visual Studio Code](https://code.visualstudio.com/). Приложение и сервер запускаются решением [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), которое активирует отладчик CoreCLR.
* [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/). Приложение и сервер запускаются отладчиком "мягкого режима" [Mono Soft Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

При запуске приложения из командной строки в папке проекта [dotnet run](/dotnet/core/tools/dotnet-run) запускает приложение и сервер (только Kestrel и HTTP.sys). Конфигурация определяется параметром `-c|--configuration`, который может принимать значение `Debug` (по умолчанию) или `Release`.

Файл *launchSettings.json* предоставляет конфигурацию при запуске приложения с помощью `dotnet run` или с помощью отладчика, встроенного в инструментарий, например Visual Studio. Если профили запуска указаны в файле *launchSettings.json*, используйте параметр `--launch-profile {PROFILE NAME}` с командой `dotnet run` или выберите профиль в Visual Studio. Дополнительные сведения см. в статьях [dotnet run](/dotnet/core/tools/dotnet-run) и [Упаковка дистрибутивов .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Поддержка HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается в ASP.NET Core для следующих сценариев развертывания:

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * Операционная система
    * Windows Server 2016 / Windows 10 или более поздних версий&dagger;
    * Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).
    * HTTP/2 будет поддерживаться для macOS в будущих выпусках.
  * Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий
  * Целевая платформа: неприменимо к развертываниям HTTP.sys.
* [IIS (внутрипроцессный)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии
  * Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней
* [IIS (внепроцессный)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии
  * Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к серверу Kestrel через обратный прокси-сервер — по протоколу HTTP/1.1.
  * Целевая платформа: неприменимо к внепроцессным развертываниям IIS.

&dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1. Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем. Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Операционная система
    * Windows Server 2016 / Windows 10 или более поздних версий&dagger;
    * Linux с OpenSSL 1.0.2 или более поздней версии (например, Ubuntu 16.04 или более поздней версии).
    * HTTP/2 будет поддерживаться для macOS в будущих выпусках.
  * Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий
  * Целевая платформа: неприменимо к развертываниям HTTP.sys.
* [IIS (внутрипроцессный)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии
  * Требуемая версия .NET Framework: .NET Core версии 2.2 или более поздней
* [IIS (внепроцессный)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии
  * Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к серверу Kestrel через обратный прокси-сервер — по протоколу HTTP/1.1.
  * Целевая платформа: неприменимо к внепроцессным развертываниям IIS.

&dagger;Для Kestrel предусмотрена ограниченная поддержка HTTP/2 в Windows Server 2012 R2 и Windows 8.1. Поддержка ограничена из-за небольшого числа поддерживаемых комплектов шифров TLS, доступных для этих операционных систем. Для обеспечения безопасности TLS-подключений может потребоваться сертификат, созданный с использованием алгоритма ECDSA.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий
  * Целевая платформа: неприменимо к развертываниям HTTP.sys.
* [IIS (внепроцессный)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016 / Windows 10 или более поздних версий; IIS 10 или более поздней версии
  * Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к серверу Kestrel через обратный прокси-сервер — по протоколу HTTP/1.1.
  * Целевая платформа: неприменимо к внепроцессным развертываниям IIS.

::: moniker-end

Подключение HTTP/2 должно использовать [согласование протокола уровня приложений (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) и TLS 1.2 или более поздней версии. Дополнительные сведения см. в разделах, о конкретных сценариях развертывания сервера.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
