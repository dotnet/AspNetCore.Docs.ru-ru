---
title: Применение политики безопасности содержимого для ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать политику безопасности содержимого (CSP) с приложениями ASP.NET Core Blazor для защиты от атак с выполнением межсайтовых сценариев (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: f44f348947e31864f5d0d44c9caf1a3aa9c3b1d4
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280337"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Применение политики безопасности содержимого для ASP.NET Core Blazor

[Выполнение межсайтовых сценариев (XSS)](xref:security/cross-site-scripting) является уязвимостью системы безопасности, когда злоумышленник помещает один или несколько вредоносных клиентских сценариев в преобразуемое для просмотра содержимое приложения. Политика безопасности содержимого (CSP) помогает защититься от атак XSS путем формирования обозревателя допустимых:

* источников загруженного содержимого, включая сценарии, таблицы стилей и изображения;
* действий, выполняемых страницей, с указанием разрешенных целевых объектов URL-адресов форм;
* подключаемых модулей, которые могут быть загружены.

Чтобы применить CSP к приложению, разработчик указывает несколько *директив* безопасности содержимого CSP в одном или нескольких заголовках `Content-Security-Policy` или тегах `<meta>`.

Политики оцениваются браузером во время загрузки страницы. Браузер проверяет источники страницы и определяет, соответствуют ли они требованиям директив безопасности содержимого. Если директивы политики не выполняются для ресурса, браузер не загружает ресурс. Например, рассмотрим политику, которая не позволяет использовать сценарии сторонних разработчиков. Если страница содержит тег `<script>`, в атрибуте `src` которого указан сторонний источник, браузер предотвращает загрузку сценария.

CSP поддерживается в большинстве современных браузеров для настольных ПК и мобильных устройств, включая Chrome, Microsoft Edge, Firefox, Opera и Safari. CSP рекомендуется для приложений Blazor.

## <a name="policy-directives"></a>Директивы политики

Как минимум, укажите следующие директивы и источники для приложений Blazor. При необходимости добавьте дополнительные директивы и источники. Следующие директивы используются в разделе [Применение политики](#apply-the-policy) этой статьи, где указаны примеры политик безопасности для Blazor WebAssembly и Blazor Server:

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): разрешает URL-адреса для тега `<base>` страницы. Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.
* [block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): предотвращает загрузку смешанного содержимого HTTP и HTTPS.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): указывает резервную форму для исходных директив, которые не задаются политикой явно. Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): указывает допустимые источники для изображений.
  * Укажите `data:`, чтобы разрешить загрузку изображений с URL-адресов `data:`.
  * Укажите `https:`, чтобы разрешить загрузку изображений с конечных точек HTTPS.
* [object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): указывает допустимые источники для тегов `<object>`, `<embed>` и `<applet>`. Укажите `none`, чтобы запретить все источники URL-адресов.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): указывает допустимые источники для сценариев.
  * Укажите источник узла `https://stackpath.bootstrapcdn.com/` для сценариев начальной загрузки.
  * Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.
  * В приложении Blazor WebAssembly:
    * Укажите хэши, чтобы разрешить загрузку необходимых сценариев.
    * Укажите `unsafe-eval`, чтобы использовать `eval()` и методы для создания кода из строк.
  * В приложении Blazor Server укажите хэши, чтобы разрешить загрузку необходимых сценариев.
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): указывает допустимые источники таблиц стилей.
  * Укажите источник узла `https://stackpath.bootstrapcdn.com/` для таблиц начальной загрузки.
  * Укажите `self`, чтобы отметить, что источник приложения, включая схему и номер порта, является допустимым источником.
  * Укажите `unsafe-inline`, чтобы разрешить использование встроенных стилей. Для пользовательского интерфейса в приложениях Blazor Server требуется встроенное объявление для повторного подключения клиента и сервера после первоначального запроса. В следующем выпуске встроенные стили могут быть удалены, поэтому `unsafe-inline` больше не требуется.
* [upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): указывает, что URL-адреса содержимого из небезопасных (HTTP) источников должны быть безопасно получены по протоколу HTTPS.

Предыдущие директивы поддерживаются всеми браузерами, кроме Microsoft Internet Explorer.

Порядок получения хэшей SHA для дополнительных встроенных сценариев:

* Примените CSP, показанный в разделе [Применение политики](#apply-the-policy).
* Доступ к консоли средств разработчика в браузере при локальном запуске приложения. Браузер вычисляет и отображает хэши для заблокированных сценариев при наличии заголовка CSP или тега `meta`.
* Скопируйте хэши, предоставленные браузером, в источники `script-src`. Заключите каждый хэш в одинарные кавычки.

Таблица поддержки браузерами политики безопасности содержимого уровня 2 приведена на сайте [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Применение политики

Используйте тег `<meta>`, чтобы применить политику:

* Присвойте атрибуту `http-equiv` значение `Content-Security-Policy`.
* Поместите директивы в значение атрибута `content`. Директивы следует разделять точкой с запятой (`;`).
* Всегда помещайте тег `meta` в содержимое `<head>`.

В следующих разделах показаны примеры политик для Blazor WebAssembly и Blazor Server. Эти примеры приведены в этой статье с контролем версий для каждого выпуска Blazor. Чтобы использовать версию, подходящую для вашего выпуска, выберите версию документа в раскрывающемся списке **Версия** на этой веб-странице.

### Blazor WebAssembly

В содержимом `<head>` страницы узла `wwwroot/index.html` примените директивы, описанные в разделе [Директивы политики](#policy-directives):

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

Добавьте дополнительные хэши `script-src` и `style-src`, необходимые этому приложению. Во время разработки используйте интерактивное средство или средства разработчика браузера, которые будут вычислять хэши. Например, следующая ошибка консоли средств браузера сообщает о том, что хэш для необходимого сценария не охвачен политикой:

> Не удается выполнить встроенный сценарий, так как он нарушает следующую директиву политики безопасности содержимого: "...". Для включения выполнения встроенного сценария требуется ключевое слово "unsafe-inline", хэш ("sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=") или nonce ("nonce-...").

Конкретный сценарий, связанный с ошибкой, отображается в консоли рядом с ошибкой.

### Blazor Server

В содержимом `<head>` страницы узла `Pages/_Host.cshtml` примените директивы, описанные в разделе [Директивы политики](#policy-directives):

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

Добавьте дополнительные хэши `script-src` и `style-src`, необходимые этому приложению. Во время разработки используйте интерактивное средство или средства разработчика браузера, которые будут вычислять хэши. Например, следующая ошибка консоли средств браузера сообщает о том, что хэш для необходимого сценария не охвачен политикой:

> Не удается выполнить встроенный сценарий, так как он нарушает следующую директиву политики безопасности содержимого: "...". Для включения выполнения встроенного сценария требуется ключевое слово "unsafe-inline", хэш ("sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=") или nonce ("nonce-...").

Конкретный сценарий, связанный с ошибкой, отображается в консоли рядом с ошибкой.

## <a name="meta-tag-limitations"></a>Ограничения метатегов

Политика тегов `<meta>` не поддерживает следующие директивы:

* [frame-ancestors](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Для поддержки предыдущих директив используйте заголовок с именем `Content-Security-Policy`. Строка директивы — это значение заголовка.

## <a name="test-a-policy-and-receive-violation-reports"></a>Тестирование политики и получение отчетов о нарушениях

Тестирование помогает убедиться в том, что сценарии сторонних разработчиков не блокируются непреднамеренно при создании начальной политики.

Чтобы протестировать политику в течение определенного периода времени без применения директив политики, задайте для атрибута`http-equiv` тега `<meta>` или заголовка политики на основе заголовков значение `Content-Security-Policy-Report-Only`. Отчеты о сбоях отправляются как документы JSON по указанному URL-адресу. Дополнительные сведения см. в [веб-документации MDN: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Сведения о создании отчетов о нарушениях, когда политика активна, см. в следующих статьях:

* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Хотя `report-uri` больше не рекомендуется использовать, обе директивы следует использовать до тех пор, пока `report-to` не будут поддерживаться всеми основными браузерами. Не используйте исключительно `report-uri`, поскольку поддержка `report-uri` может быть удалена из браузеров *в любое время*. Удалите поддержку `report-uri` в политиках, если `report-to` полностью поддерживается. Сведения об отслеживании внедрения `report-to`см. на сайте [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Протестируйте и обновляйте политику приложения в каждом выпуске.

## <a name="troubleshoot"></a>Устранение неполадок

* Ошибки отображаются в консоли средств разработчика браузера. Браузеры предоставляют следующие сведения:
  * Элементы, которые не соответствуют политике.
  * Изменение политики для разрешения заблокированного элемента.
* Политика полностью эффективна, только если браузер клиента поддерживает все включенные директивы. Таблица со сведениями о поддержке браузеров см. на сайте [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Веб-документы MDN: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Политика безопасности содержимого уровня 2](https://www.w3.org/TR/CSP2/)
* [Оценщик CSP Google](https://csp-evaluator.withgoogle.com/)
