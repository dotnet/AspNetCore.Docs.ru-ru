---
title: Включение запросов между источниками (CORS) в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать CORS в качестве стандарта для разрешения или отклонения запросов между источниками в ASP.NET Core приложении.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: b057e5e08b8a4d0f9bcd68f92102cad309655acc
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413513"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Включение запросов между источниками (CORS) в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)

В этой статье показано, как включить CORS в приложении ASP.NET Core.

Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение называется *политика одного источника*. Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта. Иногда может потребоваться разрешить другим сайтам выполнять запросы между источниками в приложении. Дополнительные сведения см. в [статье Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Общий доступ к ресурсам в разных источниках](https://www.w3.org/TR/cors/) (CORS):

* — Это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.
* **Не** является функцией безопасности, CORS ослабляет безопасность. Интерфейс API не обеспечивает безопасную поддержку CORS. Дополнительные сведения см. в разделе [как работает CORS](#how-cors).
* Позволяет серверу явно разрешать некоторые запросы между источниками и отклонять другие.
* Является более безопасным и более гибким, чем более ранние методики, например [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Тот же источник

Два URL-адреса имеют одинаковый источник, если они имеют идентичные схемы, узлы и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Эти два URL-адреса имеют один и тот же источник:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Эти URL-адреса имеют разные источники, чем предыдущие два URL-адреса:

* `https://example.net`: Другой домен
* `https://www.example.com/foo.html`: Другой поддомен
* `http://example.com/foo.html`: Другая схема
* `https://example.com:9000/foo.html`: Другой порт

## <a name="enable-cors"></a>Включение CORS

Включить CORS можно тремя способами:

* По промежуточного слоя с использованием [именованной политики](#np) или [политики по умолчанию](#dp).
* Использование [маршрутизации конечных точек](#ecors).
* С атрибутом [[EnableCors]](#attr) .

Использование атрибута [[EnableCors]](#attr) с именованной политикой предоставляет элемент управления Finest в ограничении конечных точек, поддерживающих CORS.

> [!WARNING]
> <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> должен вызываться в правильном порядке. Дополнительные сведения см. в разделе [порядок по промежуточного слоя](xref:fundamentals/middleware/index#middleware-order). Например, `UseCors` метод должен вызываться перед <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> использованием `UseResponseCaching` .

Каждый подход подробно описан в следующих разделах.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS с именованной политикой и по промежуточного слоя

По промежуточного слоя CORS обрабатывает запросы между источниками. Следующий код применяет политику CORS ко всем конечным точкам приложения с указанными источниками:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

Предыдущий код:

* Задает имя политики `_myAllowSpecificOrigins` . Имя политики является произвольным.
* Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения и задает  `_myAllowSpecificOrigins` политику CORS. `UseCors` добавляет по промежуточного слоя CORS. Вызов `UseCors` должен быть помещен после `UseRouting` , но до `UseAuthorization` . Дополнительные сведения см. в разделе [порядок по промежуточного слоя](xref:fundamentals/middleware/index#middleware-order).
* Вызывает <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Лямбда-выражение принимает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> объект. [Параметры конфигурации](#cors-policy-options), такие как `WithOrigins` , описаны далее в этой статье.
* Включает `_myAllowSpecificOrigins` политику CORS для всех конечных точек контроллера. Чтобы применить политику CORS к конкретным конечным точкам, см. раздел [Маршрутизация конечных](#ecors) точек.
* При использовании по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware)вызовите метод <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> перед <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .

При маршрутизации конечных точек по промежуточного слоя CORS **должно** быть настроено для выполнения между вызовами функций `UseRouting` и `UseEndpoints` .

Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#testc) .

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Вызов метода добавляет службы CORS в контейнер службы приложения:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Дополнительные сведения см. в разделе [Параметры политики CORS](#cpo) в этом документе.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Методы могут быть объединены в цепочку, как показано в следующем коде:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Примечание. указанный URL-адрес **не** должен содержать косую черту ( `/` ). Если URL-адрес завершается с `/` , то сравнение возвращает значение `false` и заголовок не возвращается.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS с политикой по умолчанию и по промежуточного слоя

Следующий выделенный код включает политику CORS по умолчанию:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Приведенный выше код применяет политику CORS по умолчанию ко всем конечным точкам контроллера.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Включение CORS с маршрутизацией конечных точек

Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает [Автоматические предпечатные запросы](#apf). Дополнительные сведения см. в описании [этой проблемы GitHub](https://github.com/dotnet/aspnetcore/issues/20709) и [тестировании CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer).

При маршрутизации конечных точек CORS можно включить для каждой конечной точки с помощью <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> набора методов расширения:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

В приведенном выше коде:

* `app.UseCors` включает по промежуточного слоя CORS. Так как политика по умолчанию не настроена, `app.UseCors()` она не включает CORS.
* `/echo`Конечные точки контроллера и позволяют выполнять запросы между источниками с помощью указанной политики.
* `/echo2` Razor Конечные точки страниц и **не** позволяют выполнять запросы независимо от источника, так как не указана политика по умолчанию.

Атрибут [[дисаблекорс]](#dc) **не**  отключает CORS, который был включен с помощью маршрутизации конечной точки с помощью `RequireCors` .

Инструкции по проверке кода, аналогичные приведенным выше, см. в разделе [тестирование CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer) .

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Включение CORS с помощью атрибутов

Включение CORS с помощью атрибута [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) и применение именованной политики только к тем конечным точкам, для которых требуется CORS, предоставляет элемент управления Finest.

Атрибут [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) предоставляет альтернативу глобальному применению CORS. `[EnableCors]`Атрибут включает CORS для выбранных конечных точек, а не для всех конечных точек:

* `[EnableCors]` Задает политику по умолчанию.
* `[EnableCors("{Policy String}")]` Задает именованную политику.

`[EnableCors]`Атрибут может быть применен к:

* Razor Страниц `PageModel`
* Контроллер
* Метод действия контроллера

К контроллерам, моделям страниц или методам действий можно применять различные политики с `[EnableCors]` атрибутом. Если `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, а CORS включен по промежуточного слоя, применяются **обе** политики. **Мы советуем использовать сочетание политик. Используйте** `[EnableCors]` **атрибут или по промежуточного слоя, а не оба в одном приложении.**

Следующий код применяет к каждому методу другую политику:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Следующий код создает две политики CORS:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Для Finest управления ограничением запросов CORS:

* Используйте `[EnableCors("MyPolicy")]` с именованной политикой.
* Не определяйте политику по умолчанию.
* Не используйте [маршрутизацию конечных точек](#ecors).

Код в следующем разделе соответствует приведенному выше списку.

Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#testc) .

<a name="dc"></a>

### <a name="disable-cors"></a>Отключение CORS

Атрибут [[дисаблекорс]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **не**  отключает CORS, который был включен с помощью [маршрутизации конечной точки](#ecors).

Следующий код определяет политику CORS `"MyPolicy"` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Следующий код отключает CORS для `GetValues2` действия:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Предыдущий код:

* Не включает CORS с [маршрутизацией конечных точек](#ecors).
* Не определяет [политику CORS по умолчанию](#dp).
* Использует [[EnableCors ("MyPolicy")]](#attr) , чтобы включить `"MyPolicy"` политику CORS для контроллера.
* Отключает CORS для `GetValues2` метода.

Инструкции по тестированию приведенного выше кода см. в разделе [тестирование CORS](#testc) .

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Параметры политики CORS

В этом разделе описаны различные параметры, которые можно задать в политике CORS:

* [Установка разрешенных источников](#set-the-allowed-origins)
* [Задание допустимых методов HTTP](#set-the-allowed-http-methods)
* [Задание разрешенных заголовков запроса](#set-the-allowed-request-headers)
* [Задание предоставленных заголовков ответа](#set-the-exposed-response-headers)
* [Учетные данные в запросах между источниками](#credentials-in-cross-origin-requests)
* [Задать срок действия предпечатного срока](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> метод вызывается в `Startup.ConfigureServices` . Для некоторых параметров может оказаться полезным сначала ознакомиться с разделом " [работа CORS](#how-cors) ".

## <a name="set-the-allowed-origins"></a>Установка разрешенных источников

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Разрешает запросы CORS из всех источников с любой схемой ( `http` или `https` ). `AllowAnyOrigin` не является безопасным, так как *любой веб-сайт* может выполнять запросы между источниками в приложение.

> [!NOTE]
> Указание `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке межсайтовых запросов. Служба CORS возвращает недопустимый ответ CORS, если приложение настроено с обоими методами.

`AllowAnyOrigin` влияет на предпечатные запросы и `Access-Control-Allow-Origin` заголовок. Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Задает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, которая позволяет источникам соответствовать настроенному домену с подстановочными знаками при оценке того, разрешен ли источник.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Задание допустимых методов HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Разрешает любой метод HTTP:
* Влияет на предпечатные запросы и `Access-Control-Allow-Methods` заголовок. Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Задание разрешенных заголовков запроса

Чтобы разрешить отправку конкретных заголовков в запрос CORS, именуемый [заголовком запроса на создание](https://xhr.spec.whatwg.org/#request), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> и укажите разрешенные заголовки:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Чтобы разрешить все [заголовки запроса автора](https://www.w3.org/TR/cors/#author-request-headers), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader` влияет на предпечатные запросы и заголовок [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) . Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .

Политика по промежуточного слоя CORS соответствует определенным заголовкам, указанным в параметре, `WithHeaders` только если отправляемые заголовки в `Access-Control-Request-Headers` точности совпадают с заголовками, указанными в `WithHeaders` .

Например, рассмотрим приложение, настроенное следующим образом:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

По промежуточного слоя CORS отклоняет Предпечатный запрос со следующим заголовком запроса, так как `Content-Language` ([Хеадернамес. контентлангуаже](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) нет в списке в `WithHeaders` :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Приложение возвращает ответ *ок 200* , но не ОТПРАВЛЯЕТ заголовки CORS обратно. Поэтому браузер не пытается выполнить запрос между источниками.

### <a name="set-the-exposed-response-headers"></a>Задание предоставленных заголовков ответа

По умолчанию браузер не предоставляет все заголовки ответа приложению. Дополнительные сведения см. в разделе [общий доступ к ресурсам между источниками W3C (терминология): простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).

По умолчанию доступны заголовки ответов:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Спецификация CORS вызывает эти заголовки *простых заголовков ответа*. Чтобы сделать другие заголовки доступными для приложения, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Учетные данные в запросах между источниками

Учетные данные требует специальной обработки в запросе CORS. По умолчанию браузер не отправляет учетные данные с запросом между источниками. Учетные данные включают cookie схемы проверки подлинности s и HTTP. Чтобы отправить учетные данные с запросом между источниками, клиент должен установить `XMLHttpRequest.withCredentials` в значение `true` .

Использование `XMLHttpRequest` напрямую:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Использование jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Использование [API выборки](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Сервер должен разрешать учетные данные. Чтобы разрешить учетные данные для разных источников, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP-ответ содержит `Access-Control-Allow-Credentials` заголовок, который сообщает браузеру, что сервер разрешает учетные данные для запроса между источниками.

Если браузер отправляет учетные данные, но ответ не включает допустимый `Access-Control-Allow-Credentials` заголовок, браузер не предоставляет ответ на приложение, и запрос на перекрестное происхождение завершается сбоем.

Разрешение учетных данных между источниками является угрозой безопасности. Веб-сайт в другом домене может отправить учетные данные пользователя, выполнившего вход, в приложение от имени пользователя без ведома пользователя. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

В спецификации CORS также указывается, что `"*"` при наличии заголовка недопустимыми являются исходные значения (все источники) `Access-Control-Allow-Credentials` .

<a name="pref"></a>

## <a name="preflight-requests"></a>Предпечатные запросы

Для некоторых запросов CORS браузер отправляет запрос на дополнительные [Параметры](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) перед выполнением фактического запроса. Этот запрос называется [предпечатным запросом](https://developer.mozilla.org/docs/Glossary/Preflight_request). Браузер может пропустить Предпечатный запрос, если выполняются **все** перечисленные ниже условия.

* Метод запроса — GET, HEAD или POST.
* Приложение не устанавливает заголовки запроса `Accept` , кроме, `Accept-Language` , `Content-Language` , `Content-Type` или `Last-Event-ID` .
* `Content-Type`Заголовок, если он задан, имеет одно из следующих значений:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Правило для заголовков запросов, заданных для запроса клиента, применяется к заголовкам, которые устанавливаются приложением путем вызова `setRequestHeader` для `XMLHttpRequest` объекта. Спецификация CORS вызывает заголовки [запроса автора](https://www.w3.org/TR/cors/#author-request-headers)заголовков. Правило не применяется к заголовкам, которые может задать браузер, например `User-Agent` , `Host` или `Content-Length` .

Ниже приведен пример ответа, аналогичного предпечатному запросу, выполненному с помощью кнопки **[размещение теста]** в разделе [Test CORS](#testc) этого документа.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Предпечатный запрос использует метод [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) . Он может включать следующие заголовки:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)— метод HTTP, который будет использоваться для фактического запроса.
* [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers). список заголовков запросов, которые приложение устанавливает на основе фактического запроса. Как упоминалось ранее, в него не входят заголовки, заданных браузером, например `User-Agent` .
* [Access-Control — Allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Если Предпечатный запрос отклонен, приложение возвращает `200 OK` ответ, но не задает заголовки CORS. Поэтому браузер не пытается выполнить запрос между источниками. Пример запрещенного запроса на предпечатную версию см. в разделе " [тестирование CORS](#testc) " этого документа.

С помощью средств F12 консольное приложение отображает ошибку, аналогичную одной из следующих в зависимости от браузера.

* Firefox: запрос на перекрестное происхождение заблокирован: одна и та же политика происхождения запрещает чтение удаленного ресурса в `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` . (Причина: запрос CORS не выполнен). [Подробнее](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* На основе Chromium: доступ к выборке в " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " из источника " https://cors3.azurewebsites.net " заблокирован политикой CORS: ответ на предварительный запрос не проходит проверку контроля доступа: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin". Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.

Чтобы разрешить определенные заголовки, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Чтобы разрешить все [заголовки запроса автора](https://www.w3.org/TR/cors/#author-request-headers), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Браузеры не согласуются с тем, как они заданы `Access-Control-Request-Headers` . Если:

* Для заголовков заданы любые значения, отличные от `"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> вызывается: включите как минимум `Accept` , `Content-Type` , и `Origin` , а также любые пользовательские заголовки, которые требуется поддерживать.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Код автоматического предпечатного запроса

Когда применяется политика CORS, выполните одно из следующих действий.

* Глобально путем вызова `app.UseCors` в `Startup.Configure` .
* С помощью `[EnableCors]` атрибута.

ASP.NET Core отвечает на запрос параметров предварительной проверки.

Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает автоматические предпечатные запросы.

Это поведение демонстрируется в разделе [тестирование CORS](#testc) в этом документе.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>Атрибут [Хттпоптионс] для предпечатных запросов

Когда CORS включается с соответствующей политикой, ASP.NET Core обычно отвечает на запросы на предпечатную CORS автоматически. В некоторых сценариях это может быть не так. Например, использование [CORS с маршрутизацией конечных точек](#ecors).

В следующем коде атрибут [[хттпоптионс]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) используется для создания конечных точек для запросов Options:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Инструкции по тестированию приведенного выше кода см. в статьях [тестирование CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer) .

### <a name="set-the-preflight-expiration-time"></a>Задать срок действия предпечатного срока

`Access-Control-Max-Age`Заголовок указывает время, в течение которого может быть кэширован ответ на Предпечатный запрос. Чтобы задать этот заголовок, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Принцип работы CORS

В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне HTTP-сообщений.

* CORS **не** является функцией безопасности. CORS — это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.
  * Например, вредоносный субъект может использовать [межсайтовые сценарии (XSS)](xref:security/cross-site-scripting) на сайте и выполнять межсайтовый запрос к сайту с поддержкой CORS, чтобы украсть информацию.
* API не обеспечивает безопасную поддержку CORS.
  * Для применения CORS требуется клиент (браузер). Сервер выполняет запрос и возвращает ответ. это клиент, который возвращает сообщение об ошибке и блокирует ответ. Например, в любом из следующих средств будет отображаться ответ сервера:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Веб-браузер, введя URL-адрес в адресной строке.
* Сервер может позволить обозревателям выполнять запросы API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) , которые в противном случае будут запрещены.
  * Браузеры без CORS не могут выполнять запросы между источниками. Перед CORS использовалась технология [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) для обхода этого ограничения. JSONP не использует XHR, он использует `<script>` тег для получения ответа. Скрипты могут загружаться в разных источниках.

В [спецификации CORS](https://www.w3.org/TR/cors/) появились несколько новых HTTP-заголовков, которые позволяют выполнять запросы между источниками. Если браузер поддерживает CORS, эти заголовки автоматически устанавливаются для запросов между источниками. Для включения CORS не требуется пользовательский код JavaScript.

[Кнопка "вставить тест"](https://cors3.azurewebsites.net/test) в развернутом [примере](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Ниже приведен пример запроса на перекрестное происхождение из кнопки проверки [значений](https://cors3.azurewebsites.net/) в `https://cors1.azurewebsites.net/api/values` . `Origin`Заголовок:

* Предоставляет домен сайта, выполняющего запрос.
* Параметр является обязательным и должен отличаться от узла.

**Общие заголовки**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Заголовки ответа**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Заголовки запроса**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

В `OPTIONS` запросах сервер задает заголовок заголовка **ответа** `Access-Control-Allow-Origin: {allowed origin}` в ответе. Например [,](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)запрос на нажатие кнопки [удалить [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` содержит следующие заголовки:

**Общие заголовки**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Заголовки ответа**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Заголовки запроса**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

В предыдущих **заголовках ответа** сервер задает заголовок [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) в ответе. `https://cors1.azurewebsites.net`Значение этого заголовка соответствует `Origin` заголовку запроса.

Если <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> вызывается метод, `Access-Control-Allow-Origin: *` возвращается значение с подстановочным знаком. `AllowAnyOrigin` разрешает любой источник.

Если ответ не содержит `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение завершается с ошибкой. В частности, браузер не разрешает запрос. Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным клиентскому приложению.

<a name="options"></a>

### <a name="display-options-requests"></a>Запросы параметров вывода

По умолчанию браузеры Chrome и ребра не отображают запросы параметров на вкладке Сеть средств F12. Отображение запросов OPTIONS в следующих браузерах:

* `chrome://flags/#out-of-blink-cors` или `edge://flags/#out-of-blink-cors`
* отключите флаг.
* перезагрузить.

Firefox по умолчанию отображает запросы параметров.

## <a name="cors-in-iis"></a>CORS в IIS

При развертывании в IIS CORS необходимо запустить перед проверкой подлинности Windows, если сервер не настроен на разрешение анонимного доступа. Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.

<a name="testc"></a>

## <a name="test-cors"></a>Тестирование CORS

В [примере загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) есть код для тестирования CORS. См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample). Пример представляет собой проект API с Razor добавленными страницами:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` следует использовать только для тестирования примера приложения, аналогичного [образцу кода для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).

Ниже `ValuesController` приведены конечные точки для тестирования.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[Мидисплайраутеинфо](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) предоставляется пакетом NuGet [Rick.Docs. Samples. раутеинфо](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) и отображает сведения о маршруте.

Протестируйте предыдущий пример кода с помощью одного из следующих подходов:

* Используйте развернутый пример приложения в [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) . Нет необходимости скачивать пример.
* Запустите пример с `dotnet run` использованием URL-адреса по умолчанию для `https://localhost:5001` .
* Запустите пример из Visual Studio с портом 44398 для URL-адреса `https://localhost:44398` .

Использование браузера с инструментами F12:

* Нажмите кнопку **значения** и проверьте заголовки на вкладке **сеть** .
* Нажмите кнопку **Вставить тест** . Инструкции по отображению запроса OPTIONS см. в разделе [Параметры отображения запросы](#options) . **Тест размещения** создает два запроса: запрос на предварительную проверку параметров и запрос на размещение.
* Нажмите **`GetValues2 [DisableCors]`** кнопку, чтобы активировать неудачный запрос CORS. Как упоминалось в документе, ответ возвращает значение 200, но запрос CORS не выполняется. Перейдите на вкладку **Console (консоль** ), чтобы просмотреть ошибку CORS. В зависимости от браузера отображается сообщение об ошибке следующего вида:

     Доступ к выборке `'https://cors1.azurewebsites.net/api/values/GetValues2'` из источника `'https://cors3.azurewebsites.net'` заблокирован политикой CORS: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin". Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.
     
Конечные точки с поддержкой CORS можно тестировать с помощью средства, такого [как](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler), или [POST](https://www.getpostman.com/). При использовании средства источник запроса, указанный в `Origin` заголовке, должен отличаться от узла, получающего запрос. Если запрос не имеет *источника* на основе значения `Origin` заголовка:

* Для обработки запроса по промежуточного слоя CORS не требуется.
* Заголовки CORS не возвращаются в ответе.

Следующая команда использует `curl` , чтобы выдать запрос Options с информацией:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Тестирование CORS с маршрутизацией конечных точек и [Хттпоптионс]

Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает [Автоматические предпечатные запросы](#apf). Рассмотрим следующий код, который использует [маршрутизацию конечных точек для включения CORS](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Ниже `TodoItems1Controller` приведены конечные точки для тестирования.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Протестируйте предыдущий код на [странице тест](https://cors1.azurewebsites.net/test?number=1) в развернутом [примере](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI).

Кнопки **Delete [EnableCors]** и **Get [EnableCors]** выполняются успешно, так как конечные точки имеют `[EnableCors]` и отвечают на предпечатные запросы. Другие конечные точки не удается. Не удается выполнить кнопку **Get** , так как [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) отправляет:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Ниже `TodoItems2Controller` приведены аналогичные конечные точки, но включается явный код для реагирования на запросы Options:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Протестируйте предыдущий код на [странице тест](https://cors1.azurewebsites.net/test?number=2) в развернутом примере. В раскрывающемся списке **контроллер** выберите Предварительная **Проверка** , а затем **Задайте значение контроллер**. Все вызовы CORS к `TodoItems2Controller` конечным точкам выполнены.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Общий доступ к ресурсам между источниками (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Приступая к работе с модулем IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

В этой статье показано, как включить CORS в приложении ASP.NET Core.

Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу. Это ограничение называется *политика одного источника*. Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта. Иногда может потребоваться разрешить другим сайтам выполнять запросы между источниками в приложении. Дополнительные сведения см. в [статье Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Общий доступ к ресурсам в разных источниках](https://www.w3.org/TR/cors/) (CORS):

* — Это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.
* **Не** является функцией безопасности, CORS ослабляет безопасность. Интерфейс API не обеспечивает безопасную поддержку CORS. Дополнительные сведения см. в разделе [как работает CORS](#how-cors).
* Позволяет серверу явно разрешать некоторые запросы между источниками и отклонять другие.
* Является более безопасным и более гибким, чем более ранние методики, например [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Тот же источник

Два URL-адреса имеют одинаковый источник, если они имеют идентичные схемы, узлы и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Эти два URL-адреса имеют один и тот же источник:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Эти URL-адреса имеют разные источники, чем предыдущие два URL-адреса:

* `https://example.net`: Другой домен
* `https://www.example.com/foo.html`: Другой поддомен
* `http://example.com/foo.html`: Другая схема
* `https://example.com:9000/foo.html`: Другой порт

При сравнении источников Internet Explorer не учитывает порт.

## <a name="cors-with-named-policy-and-middleware"></a>CORS с именованной политикой и по промежуточного слоя

По промежуточного слоя CORS обрабатывает запросы между источниками. Следующий код включает CORS для всего приложения с указанным источником:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Предыдущий код:

* Задает имя политики " \_ мялловспеЦификоригинс". Имя политики является произвольным.
* Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения, который включает CORS.
* Вызывает <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Лямбда-выражение принимает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> объект. [Параметры конфигурации](#cors-policy-options), такие как `WithOrigins` , описаны далее в этой статье.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Вызов метода добавляет службы CORS в контейнер службы приложения:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Дополнительные сведения см. в разделе [Параметры политики CORS](#cpo) в этом документе.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Метод может привести к цепочке методов, как показано в следующем коде:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Примечание. URL-адрес **не** должен содержать косую черту ( `/` ). Если URL-адрес завершается с `/` , то сравнение возвращает значение `false` и заголовок не возвращается.

Следующий код применяет политики CORS ко всем конечным точкам приложений через по промежуточного слоя CORS:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Примечание. `UseCors` необходимо вызвать до `UseMvc` .

См. раздел [Включение CORS на Razor страницах, контроллерах и методах действий](#ecors) для применения политики CORS на уровне страницы, контроллера или действия.

Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#test) .

## <a name="enable-cors-with-attributes"></a>Включение CORS с помощью атрибутов

Атрибут [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) предоставляет альтернативу глобальному применению CORS. `[EnableCors]`Атрибут включает CORS для выбранных конечных точек, а не всех конечных точек.

Используйте `[EnableCors]` , чтобы указать политику по умолчанию и `[EnableCors("{Policy String}")]` указать политику.

`[EnableCors]`Атрибут может быть применен к:

* Razor Страниц `PageModel`
* Контроллер
* Метод действия контроллера

С атрибутом можно применить различные политики для контроллера, модели страницы или действия  `[EnableCors]` . Если `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, а CORS включен по промежуточного слоя, применяются **обе** политики. **Не** рекомендуется объединять политики. Используйте `[EnableCors]` атрибут или по промежуточного слоя, а **не оба**. При использовании не `[EnableCors]` определяйте  политику по умолчанию.

Следующий код применяет к каждому методу другую политику:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Следующий код создает политику CORS по умолчанию и политику с именем `"AnotherPolicy"` :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Отключение CORS

Атрибут [ &lbrack; ДИСАБЛЕКОРС &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) отключает CORS для контроллера, модели страницы или действия.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Параметры политики CORS

В этом разделе описаны различные параметры, которые можно задать в политике CORS:

* [Установка разрешенных источников](#set-the-allowed-origins)
* [Задание допустимых методов HTTP](#set-the-allowed-http-methods)
* [Задание разрешенных заголовков запроса](#set-the-allowed-request-headers)
* [Задание предоставленных заголовков ответа](#set-the-exposed-response-headers)
* [Учетные данные в запросах между источниками](#credentials-in-cross-origin-requests)
* [Задать срок действия предпечатного срока](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> метод вызывается в `Startup.ConfigureServices` . Для некоторых параметров может оказаться полезным сначала ознакомиться с разделом " [работа CORS](#how-cors) ".

## <a name="set-the-allowed-origins"></a>Установка разрешенных источников

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Разрешает запросы CORS из всех источников с любой схемой ( `http` или `https` ). `AllowAnyOrigin` не является безопасным, так как *любой веб-сайт* может выполнять запросы между источниками в приложение.

> [!NOTE]
> Указание `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке межсайтовых запросов. Для безопасного приложения укажите точный список источников, если клиент должен авторизоваться для доступа к ресурсам сервера.

`AllowAnyOrigin` влияет на предпечатные запросы и `Access-Control-Allow-Origin` заголовок. Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Задает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, которая позволяет источникам соответствовать настроенному домену с подстановочными знаками при оценке того, разрешен ли источник.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Задание допустимых методов HTTP

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Разрешает любой метод HTTP:
* Влияет на предпечатные запросы и `Access-Control-Allow-Methods` заголовок. Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .

### <a name="set-the-allowed-request-headers"></a>Задание разрешенных заголовков запроса

Чтобы разрешить отправку конкретных заголовков в запрос CORS, именуемый *заголовком запроса на создание*, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> и укажите разрешенные заголовки:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Чтобы разрешить все заголовки запроса автора, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Этот параметр влияет на предпечатные запросы и `Access-Control-Request-Headers` заголовок. Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .

По промежуточного слоя CORS всегда позволяет отправлять четыре заголовка в, `Access-Control-Request-Headers` независимо от значений, настроенных в корсполици. Headers. Этот список заголовков включает:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Например, рассмотрим приложение, настроенное следующим образом:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

По промежуточного слоя CORS успешно реагирует на предварительный запрос со следующим заголовком запроса, так как `Content-Language` всегда разрешено следующее:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Задание предоставленных заголовков ответа

По умолчанию браузер не предоставляет все заголовки ответа приложению. Дополнительные сведения см. в разделе [общий доступ к ресурсам между источниками W3C (терминология): простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).

По умолчанию доступны заголовки ответов:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Спецификация CORS вызывает эти заголовки *простых заголовков ответа*. Чтобы сделать другие заголовки доступными для приложения, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Учетные данные в запросах между источниками

Учетные данные требует специальной обработки в запросе CORS. По умолчанию браузер не отправляет учетные данные с запросом между источниками. Учетные данные включают cookie схемы проверки подлинности s и HTTP. Чтобы отправить учетные данные с запросом между источниками, клиент должен установить `XMLHttpRequest.withCredentials` в значение `true` .

Использование `XMLHttpRequest` напрямую:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Использование jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Использование [API выборки](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Сервер должен разрешать учетные данные. Чтобы разрешить учетные данные для разных источников, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP-ответ содержит `Access-Control-Allow-Credentials` заголовок, который сообщает браузеру, что сервер разрешает учетные данные для запроса между источниками.

Если браузер отправляет учетные данные, но ответ не включает допустимый `Access-Control-Allow-Credentials` заголовок, браузер не предоставляет ответ на приложение, и запрос на перекрестное происхождение завершается сбоем.

Разрешение учетных данных между источниками является угрозой безопасности. Веб-сайт в другом домене может отправить учетные данные пользователя, выполнившего вход, в приложение от имени пользователя без ведома пользователя. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

В спецификации CORS также указывается, что `"*"` при наличии заголовка недопустимыми являются исходные значения (все источники) `Access-Control-Allow-Credentials` .

### <a name="preflight-requests"></a>Предпечатные запросы

Для некоторых запросов CORS браузер отправляет дополнительный запрос перед выполнением фактического запроса. Этот запрос называется *предпечатным запросом*. Браузер может пропустить Предпечатный запрос, если выполняются следующие условия.

* Метод запроса — GET, HEAD или POST.
* Приложение не устанавливает заголовки запроса `Accept` , кроме, `Accept-Language` , `Content-Language` , `Content-Type` или `Last-Event-ID` .
* `Content-Type`Заголовок, если он задан, имеет одно из следующих значений:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Правило для заголовков запросов, заданных для запроса клиента, применяется к заголовкам, которые устанавливаются приложением путем вызова `setRequestHeader` для `XMLHttpRequest` объекта. Спецификация CORS вызывает заголовки *запроса автора* заголовков. Правило не применяется к заголовкам, которые может задать браузер, например `User-Agent` , `Host` или `Content-Length` .

Ниже приведен пример предпечатного запроса.

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Запрос перед рейсом использует метод HTTP OPTIONS. Он включает два специальных заголовка:

* `Access-Control-Request-Method`: Метод HTTP, который будет использоваться для фактического запроса.
* `Access-Control-Request-Headers`: Список заголовков запросов, которые приложение задает для фактического запроса. Как упоминалось ранее, в него не входят заголовки, заданных браузером, например `User-Agent` .

<!-- I think this needs to be removed, was put here accidently -->

При включении CORS с соответствующей политикой ASP.NET Core обычно автоматически реагирует на предпечатные запросы CORS. См. [атрибут [хттпоптионс] для предпечатных запросов](#pro).

Предпечатный запрос CORS может включать `Access-Control-Request-Headers` заголовок, указывающий серверу на заголовки, отправляемые с фактическим запросом.

Чтобы разрешить определенные заголовки, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Чтобы разрешить все заголовки запроса автора, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Браузеры не полностью согласуются с тем, как они заданы `Access-Control-Request-Headers` . Если для заголовков задано значение `"*"` , отличное от (или использование <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), следует включить как минимум `Accept` , `Content-Type` , и `Origin` , а также любые настраиваемые заголовки, которые требуется поддерживать.

Ниже приведен пример ответа на Предпечатный запрос (при условии, что сервер разрешает запрос):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Ответ содержит `Access-Control-Allow-Methods` заголовок со списком допустимых методов и, при необходимости `Access-Control-Allow-Headers` , заголовок, в котором перечислены разрешенные заголовки. Если Предпечатный запрос выполнен, браузер отправляет фактический запрос.

Если Предпечатный запрос отклонен, приложение возвращает ответ *ок 200* , но не ОТПРАВЛЯЕТ заголовки CORS обратно. Поэтому браузер не пытается выполнить запрос между источниками.

### <a name="set-the-preflight-expiration-time"></a>Задать срок действия предпечатного срока

`Access-Control-Max-Age`Заголовок указывает время, в течение которого может быть кэширован ответ на Предпечатный запрос. Чтобы задать этот заголовок, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Принцип работы CORS

В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне HTTP-сообщений.

* CORS **не** является функцией безопасности. CORS — это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.
  * Например, вредоносный субъект может использовать для веб [-узла предотвращение межсайтовых сценариев (XSS)](xref:security/cross-site-scripting) и выполнить межсайтовый запрос к сайту с поддержкой CORS, чтобы украсть информацию.
* Интерфейс API не обеспечивает безопасную работу, разрешая CORS.
  * Для применения CORS требуется клиент (браузер). Сервер выполняет запрос и возвращает ответ. это клиент, который возвращает сообщение об ошибке и блокирует ответ. Например, в любом из следующих средств будет отображаться ответ сервера:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [HttpClient .NET](/dotnet/csharp/tutorials/console-webapiclient)
    * Веб-браузер, введя URL-адрес в адресной строке.
* Сервер может позволить обозревателям выполнять запросы API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) , которые в противном случае будут запрещены.
  * Браузеры (без CORS) не могут выполнять запросы между источниками. Перед CORS использовалась технология [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) для обхода этого ограничения. JSONP не использует XHR, он использует `<script>` тег для получения ответа. Скрипты могут загружаться в разных источниках.

В [спецификации CORS](https://www.w3.org/TR/cors/) появились несколько новых HTTP-заголовков, которые позволяют выполнять запросы между источниками. Если браузер поддерживает CORS, эти заголовки автоматически устанавливаются для запросов между источниками. Для включения CORS не требуется пользовательский код JavaScript.

Ниже приведен пример запроса между источниками. `Origin`Заголовок предоставляет домен сайта, выполняющего запрос. `Origin`Заголовок является обязательным и должен отличаться от узла.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Если сервер разрешает запрос, он устанавливает `Access-Control-Allow-Origin` заголовок в ответе. Значение этого заголовка либо соответствует `Origin` заголовку запроса, либо является подстановочным значением `"*"` , что означает, что любой источник разрешен:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Если ответ не содержит `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение завершается с ошибкой. В частности, браузер не разрешает запрос. Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным клиентскому приложению.

<a name="test"></a>

## <a name="test-cors"></a>Тестирование CORS

Тестирование CORS:

1. [Создайте проект API](xref:tutorials/first-web-api). Кроме того, можно [загрузить пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors).
1. Включите CORS с помощью одного из подходов, описанных в этом документе. Пример:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` следует использовать только для тестирования примера приложения, аналогичного [образцу кода для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Создайте проект веб-приложения ( Razor страницы или MVC). В образце используются Razor страницы. Вы можете создать веб-приложение в том же решении, что и проект API.
1. Добавьте выделенный ниже код в файл *index. cshtml* :

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. В приведенном выше коде замените `url: 'https://<web app>.azurewebsites.net/api/values/1',` URL-адресом развернутого приложения.
1. Разверните проект API. Например, выполните [развертывание в Azure](xref:host-and-deploy/azure-apps/index).
1. Запустите Razor страницы или приложение MVC на рабочем столе и нажмите кнопку **Test (тест** ). Используйте средства F12 для просмотра сообщений об ошибках.
1. Удалите источник localhost из `WithOrigins` и разверните приложение. Кроме того, можно запустить клиентское приложение с другим портом. Например, запустите из Visual Studio.
1. Протестируйте клиентское приложение. Ошибки CORS возвращают ошибку, но сообщение об ошибке недоступно для JavaScript. Чтобы просмотреть ошибку, используйте вкладку консоль в средствах F12. В зависимости от браузера вы получаете ошибку (в консоли средств F12), как показано ниже:

   * Использование Microsoft ребр:

     **SEC7120: [CORS] `https://localhost:44375` не удалось найти источник `https://localhost:44375` в заголовке ответа Access-Control-Allow-Origin для ресурса-источника в `https://webapi.azurewebsites.net/api/values/1`**

   * Использование Chrome:

     **Доступ к XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` из источника `https://localhost:44375` заблокирован политикой CORS: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".**
     
Конечные точки с поддержкой CORS можно тестировать с помощью средства, такого как [Fiddler](https://www.telerik.com/fiddler) или [POST](https://www.getpostman.com/). При использовании средства источник запроса, указанный в `Origin` заголовке, должен отличаться от узла, получающего запрос. Если запрос не имеет *источника* на основе значения `Origin` заголовка:

* Для обработки запроса по промежуточного слоя CORS не требуется.
* Заголовки CORS не возвращаются в ответе.

## <a name="cors-in-iis"></a>CORS в IIS

При развертывании в IIS CORS необходимо запустить перед проверкой подлинности Windows, если сервер не настроен на разрешение анонимного доступа. Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Общий доступ к ресурсам между источниками (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Приступая к работе с модулем IIS CORS](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
