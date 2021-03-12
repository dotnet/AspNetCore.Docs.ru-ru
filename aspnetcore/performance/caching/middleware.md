---
title: Кэширование ответа по промежуточного слоя в ASP.NET Core
author: rick-anderson
description: Узнайте, как настроить и использовать ПО промежуточного слоя для кэширование ответов в ASP.NET Core.
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
uid: performance/caching/middleware
ms.openlocfilehash: 0f1f5dfcb9595270a9659a02141f7d1eba5c44ef
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587701"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Кэширование ответа по промежуточного слоя в ASP.NET Core

Автор: [John Luo](https://github.com/JunTaoLuo) (Джон Луо)

::: moniker range=">= aspnetcore-3.0"

В этой статье объясняется, как настроить по промежуточного слоя кэширования ответов в приложении ASP.NET Core. По промежуточного слоя определяет, когда ответы кэшируются, сохраняет ответы и обслуживает ответы из кэша. Общие сведения о кэшировании HTTP и [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) атрибуте см. в разделе [кэширование ответов](xref:performance/caching/response).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Конфигурация

По промежуточного слоя кэширования ответов неявным образом доступно для ASP.NET Core приложений через общую платформу.

В `Startup.ConfigureServices` добавьте по промежуточного слоя кэширования ответа в коллекцию служб:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Настройте приложение для использования по промежуточного слоя с помощью <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> метода расширения, который добавляет по промежуточного слоя в конвейер обработки запросов в `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A> должен вызываться перед <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> использованием по [промежуточного слоя CORS](xref:security/cors).

Пример приложения добавляет заголовки для управления кэшированием последующих запросов:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): кэширует ответы в кэше до 10 секунд.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): настраивает по промежуточного слоя для обслуживания кэшированного ответа только в том случае, если заголовок [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) последующих запросов совпадает с заголовком исходного запроса.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Предыдущие заголовки не записываются в ответ и переопределяются при работе контроллера, действия или Razor страницы:

* Имеет атрибут [[респонсекаче]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Это применимо, даже если свойство не задано. Например, пропуск свойства [варибихеадер](./response.md#vary) приведет к удалению соответствующего заголовка из ответа.

По промежуточного слоя кэширования ответов кэширует только ответы сервера, приводящие к коду состояния 200 (ОК). Любые другие ответы, включая [страницы ошибок](xref:fundamentals/error-handling), по промежуточного слоя игнорируются.

> [!WARNING]
> Ответы, содержащие содержимое для прошедших проверку клиентов, должны быть помечены как недоступные для кэширования, чтобы предотвратить хранение и обслуживание этих ответов по промежуточного слоя. Сведения о том, как по промежуточного слоя определяет, является ли ответ кэшированным, см. в разделе [условия кэширования](#conditions-for-caching) .

## <a name="options"></a>Варианты

Параметры кэширования ответов приведены в следующей таблице.

| Параметр | Описание |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Самый крупный размер кэша для текста ответа в байтах. Значение по умолчанию — `64 * 1024 * 1024` (64 МБ). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Предельный размер для по промежуточного слоя кэша ответов в байтах. Значение по умолчанию — `100 * 1024 * 1024` (100 МБ). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Определяет, кэшируются ли ответы в путях с учетом регистра. Значение по умолчанию — `false`. |

В следующем примере по промежуточного слоя настраивается:

* Ответы кэша с размером текста меньше или равным 1 024 байт.
* Храните ответы по путям с учетом регистра. Например, `/page1` и `/Page1` хранятся отдельно.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>варибикуерикэйс

При использовании моделей MVC, веб-API или страниц Razor страниц, [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) атрибут задает параметры, необходимые для установки соответствующих заголовков для кэширования ответов. Единственный параметр `[ResponseCache]` атрибута, для которого строго требуется по промежуточного слоя <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , не соответствует фактическому заголовку HTTP. Для получения дополнительной информации см. <xref:performance/caching/response#responsecache-attribute>.

Если атрибут не используется `[ResponseCache]` , кэширование ответов можно изменять с помощью `VaryByQueryKeys` . Используйте <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> непосредственно из [функции HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

При использовании одного значения, равного, `*` `VaryByQueryKeys` аргумент Cache изменяется в зависимости от всех параметров запроса запроса.

## <a name="http-headers-used-by-response-caching-middleware"></a>Заголовки HTTP, используемые по промежуточного слоя кэширования ответов

В следующей таблице приведены сведения о заголовках HTTP, влияющих на кэширование ответов.

| Заголовок | Сведения |
| ------ | ------- |
| `Authorization` | Ответ не кэшируется, если заголовок существует. |
| `Cache-Control` | По промежуточного слоя рассматривает только ответы кэширования, отмеченные `public` директивой Cache. Управление кэшированием со следующими параметрами:<ul><li>максимальный возраст</li><li>максимальный — устаревший&#8224;</li><li>min-свежая</li><li>must-revalidate</li><li>no-cache</li><li>без магазина</li><li>только в случае кэширования</li><li>private</li><li>public</li><li>s-maxage</li><li>прокси — повторная проверка&#8225;</li></ul>&#8224;если ограничение не задано `max-stale` , по промежуточного слоя не выполняет никаких действий.<br>&#8225;`proxy-revalidate` имеет тот же результат, что и `must-revalidate` .<br><br>Дополнительные сведения см. в разделе [RFC 7231: Request Cache-Control директивы](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`Заголовок в запросе дает тот же результат, что и `Cache-Control: no-cache` . Этот заголовок переопределяется соответствующими директивами в `Cache-Control` заголовке, если он имеется. Рассматривается для обеспечения обратной совместимости с HTTP/1.0. |
| `Set-Cookie` | Ответ не кэшируется, если заголовок существует. Любое по промежуточного слоя в конвейере обработки запросов, которое задает один или несколько объектов, cookie предотвращает кэширование ответа по промежуточного слоя (например, [ cookie поставщик TempData на основе](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary`Заголовок используется для изменения кэшированного ответа другим заголовком. Например, ответы кэшируются по кодировке путем включения `Vary: Accept-Encoding` заголовка, который кэширует ответы для запросов с заголовками `Accept-Encoding: gzip` и по `Accept-Encoding: text/plain` отдельности. Ответ со значением заголовка объекта `*` никогда не сохраняется. |
| `Expires` | Ответ, который считается устаревшим по этому заголовку, не сохраняется или не извлекается, если он не переопределен другими `Cache-Control` заголовками. |
| `If-None-Match` | Полный ответ обрабатывается из кэша, если значение не равно, `*` а `ETag` ответ не совпадает ни с одним из указанных значений. В противном случае выдается ответ 304 (не изменено). |
| `If-Modified-Since` | Если `If-None-Match` заголовок отсутствует, полный ответ передается из кэша, если значение кэшированной даты ответа новее заданного значения. В противном случае обслуживается ответ *304 — не изменено* . |
| `Date` | При обслуживании из кэша `Date` заголовок задается по промежуточного слоя, если оно не было указано в исходном ответе. |
| `Content-Length` | При обслуживании из кэша `Content-Length` заголовок задается по промежуточного слоя, если оно не было указано в исходном ответе. |
| `Age` | `Age`Заголовок, отправленный в исходном ответе, игнорируется. По промежуточного слоя выполняет вычисление нового значения при обработке кэшированного ответа. |

## <a name="caching-respects-request-cache-control-directives"></a>Кэширование учитывает директивы Cache-Control запросов

По промежуточного слоя учитывает правила [спецификации кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Для правил требуется, чтобы кэш учитывал допустимый `Cache-Control` заголовок, отправленный клиентом. В соответствии со спецификацией клиент может выполнять запросы со `no-cache` значением заголовка и принудительно создавать новый ответ для каждого запроса. В настоящее время разработчик не управляет этим поведением кэширования при использовании по промежуточного слоя, поскольку по промежуточного слоя соответствует официальной спецификации кэширования.

Чтобы получить более полный контроль над поведением кэширования, изучите другие функции кэширования ASP.NET Core. См. следующие статьи:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Диагностика

Если поведение кэширования не так, как ожидалось, убедитесь, что ответы кэшируются и могут обрабатываться из кэша. Изучите Входящие заголовки запроса и исходящие заголовки ответа. Включите [ведение журнала](xref:fundamentals/logging/index) для помощи при отладке.

При тестировании и устранении неполадок кэширования браузер может задавать заголовки запросов, которые влияют на кэширование нежелательным образом. Например, браузер может задать `Cache-Control` для заголовка `no-cache` или `max-age=0` при обновлении страницы. Следующие средства могут явно задавать заголовки запроса и являются предпочтительными для тестирования кэширования.

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Условия для кэширования

* Запрос должен привести к отклику сервера с кодом состояния 200 (ОК).
* Метод запроса должен быть GET или HEAD.
* В `Startup.Configure` по промежуточного слоя, для которого требуется кэширование, должно быть помещено в состояние кэширования ответа. Для получения дополнительной информации см. <xref:fundamentals/middleware/index>.
* `Authorization`Заголовок не должен присутствовать.
* `Cache-Control` параметры заголовка должны быть допустимыми, и ответ должен быть помечен `public` и не помечен `private` .
* `Pragma: no-cache`Заголовок не должен присутствовать `Cache-Control` , если заголовок отсутствует, так как `Cache-Control` заголовок переопределяет `Pragma` заголовок при его наличии.
* `Set-Cookie`Заголовок не должен присутствовать.
* `Vary` параметры заголовка должны быть допустимыми и не равны `*` .
* `Content-Length`Значение заголовка (если оно задано) должно соответствовать размеру текста ответа.
* Объект <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> не используется.
* Ответ не должен быть устаревшим, как указано в `Expires` заголовках и `max-age` `s-maxage` директивах кэша и.
* Буферизация ответов должна быть успешной. Размер ответа должен быть меньше заданного значения или по умолчанию <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Размер текста ответа должен быть меньше заданного значения или по умолчанию <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Ответ должен быть кэширован согласно спецификациям [RFC 7234](https://tools.ietf.org/html/rfc7234) . Например, `no-store` директива не должна существовать в полях заголовка запроса или ответа. Дополнительные сведения см. *в разделе 3. хранение ответов в кэшах* [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Система защиты от подделки для создания безопасных маркеров для предотвращения подделки межсайтовых запросов (CSRF) устанавливает `Cache-Control` заголовки и `Pragma` `no-cache` так, чтобы ответы не кэшируются. Сведения об отключении маркеров подделки для элементов HTML-форм см. в разделе <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этой статье объясняется, как настроить по промежуточного слоя кэширования ответов в приложении ASP.NET Core. По промежуточного слоя определяет, когда ответы кэшируются, сохраняет ответы и обслуживает ответы из кэша. Общие сведения о кэшировании HTTP и [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) атрибуте см. в разделе [кэширование ответов](xref:performance/caching/response).

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Конфигурация

Используйте [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) или добавьте ссылку на пакет [Microsoft. AspNetCore. респонсекачинг](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) .

В `Startup.ConfigureServices` добавьте по промежуточного слоя кэширования ответа в коллекцию служб:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Настройте приложение для использования по промежуточного слоя с помощью <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> метода расширения, который добавляет по промежуточного слоя в конвейер обработки запросов в `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Пример приложения добавляет заголовки для управления кэшированием последующих запросов:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): кэширует ответы в кэше до 10 секунд.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): настраивает по промежуточного слоя для обслуживания кэшированного ответа только в том случае, если заголовок [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) последующих запросов совпадает с заголовком исходного запроса.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Предыдущие заголовки не записываются в ответ и переопределяются при работе контроллера, действия или Razor страницы:

* Имеет атрибут [[респонсекаче]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) . Это применимо, даже если свойство не задано. Например, пропуск свойства [варибихеадер](./response.md#vary) приведет к удалению соответствующего заголовка из ответа.

По промежуточного слоя кэширования ответов кэширует только ответы сервера, приводящие к коду состояния 200 (ОК). Любые другие ответы, включая [страницы ошибок](xref:fundamentals/error-handling), по промежуточного слоя игнорируются.

> [!WARNING]
> Ответы, содержащие содержимое для прошедших проверку клиентов, должны быть помечены как недоступные для кэширования, чтобы предотвратить хранение и обслуживание этих ответов по промежуточного слоя. Сведения о том, как по промежуточного слоя определяет, является ли ответ кэшированным, см. в разделе [условия кэширования](#conditions-for-caching) .

## <a name="options"></a>Варианты

Параметры кэширования ответов приведены в следующей таблице.

| Параметр | Описание |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Самый крупный размер кэша для текста ответа в байтах. Значение по умолчанию — `64 * 1024 * 1024` (64 МБ). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Предельный размер для по промежуточного слоя кэша ответов в байтах. Значение по умолчанию — `100 * 1024 * 1024` (100 МБ). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Определяет, кэшируются ли ответы в путях с учетом регистра. Значение по умолчанию — `false`. |

В следующем примере по промежуточного слоя настраивается:

* Ответы кэша с размером текста меньше или равным 1 024 байт.
* Храните ответы по путям с учетом регистра. Например, `/page1` и `/Page1` хранятся отдельно.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>варибикуерикэйс

При использовании моделей MVC, веб-API или страниц Razor страниц, [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) атрибут задает параметры, необходимые для установки соответствующих заголовков для кэширования ответов. Единственный параметр `[ResponseCache]` атрибута, для которого строго требуется по промежуточного слоя <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , не соответствует фактическому заголовку HTTP. Для получения дополнительной информации см. <xref:performance/caching/response#responsecache-attribute>.

Если атрибут не используется `[ResponseCache]` , кэширование ответов можно изменять с помощью `VaryByQueryKeys` . Используйте <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> непосредственно из [функции HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

При использовании одного значения, равного, `*` `VaryByQueryKeys` аргумент Cache изменяется в зависимости от всех параметров запроса запроса.

## <a name="http-headers-used-by-response-caching-middleware"></a>Заголовки HTTP, используемые по промежуточного слоя кэширования ответов

В следующей таблице приведены сведения о заголовках HTTP, влияющих на кэширование ответов.

| Заголовок | Сведения |
| ------ | ------- |
| `Authorization` | Ответ не кэшируется, если заголовок существует. |
| `Cache-Control` | По промежуточного слоя рассматривает только ответы кэширования, отмеченные `public` директивой Cache. Управление кэшированием со следующими параметрами:<ul><li>максимальный возраст</li><li>максимальный — устаревший&#8224;</li><li>min-свежая</li><li>must-revalidate</li><li>no-cache</li><li>без магазина</li><li>только в случае кэширования</li><li>private</li><li>public</li><li>s-maxage</li><li>прокси — повторная проверка&#8225;</li></ul>&#8224;если ограничение не задано `max-stale` , по промежуточного слоя не выполняет никаких действий.<br>&#8225;`proxy-revalidate` имеет тот же результат, что и `must-revalidate` .<br><br>Дополнительные сведения см. в разделе [RFC 7231: Request Cache-Control директивы](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`Заголовок в запросе дает тот же результат, что и `Cache-Control: no-cache` . Этот заголовок переопределяется соответствующими директивами в `Cache-Control` заголовке, если он имеется. Рассматривается для обеспечения обратной совместимости с HTTP/1.0. |
| `Set-Cookie` | Ответ не кэшируется, если заголовок существует. Любое по промежуточного слоя в конвейере обработки запросов, которое задает один или несколько объектов, cookie предотвращает кэширование ответа по промежуточного слоя (например, [ cookie поставщик TempData на основе](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | `Vary`Заголовок используется для изменения кэшированного ответа другим заголовком. Например, ответы кэшируются по кодировке путем включения `Vary: Accept-Encoding` заголовка, который кэширует ответы для запросов с заголовками `Accept-Encoding: gzip` и по `Accept-Encoding: text/plain` отдельности. Ответ со значением заголовка объекта `*` никогда не сохраняется. |
| `Expires` | Ответ, который считается устаревшим по этому заголовку, не сохраняется или не извлекается, если он не переопределен другими `Cache-Control` заголовками. |
| `If-None-Match` | Полный ответ обрабатывается из кэша, если значение не равно, `*` а `ETag` ответ не совпадает ни с одним из указанных значений. В противном случае выдается ответ 304 (не изменено). |
| `If-Modified-Since` | Если `If-None-Match` заголовок отсутствует, полный ответ передается из кэша, если значение кэшированной даты ответа новее заданного значения. В противном случае обслуживается ответ *304 — не изменено* . |
| `Date` | При обслуживании из кэша `Date` заголовок задается по промежуточного слоя, если оно не было указано в исходном ответе. |
| `Content-Length` | При обслуживании из кэша `Content-Length` заголовок задается по промежуточного слоя, если оно не было указано в исходном ответе. |
| `Age` | `Age`Заголовок, отправленный в исходном ответе, игнорируется. По промежуточного слоя выполняет вычисление нового значения при обработке кэшированного ответа. |

## <a name="caching-respects-request-cache-control-directives"></a>Кэширование учитывает директивы Cache-Control запросов

По промежуточного слоя учитывает правила [спецификации кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234#section-5.2). Для правил требуется, чтобы кэш учитывал допустимый `Cache-Control` заголовок, отправленный клиентом. В соответствии со спецификацией клиент может выполнять запросы со `no-cache` значением заголовка и принудительно создавать новый ответ для каждого запроса. В настоящее время разработчик не управляет этим поведением кэширования при использовании по промежуточного слоя, поскольку по промежуточного слоя соответствует официальной спецификации кэширования.

Чтобы получить более полный контроль над поведением кэширования, изучите другие функции кэширования ASP.NET Core. См. следующие статьи:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Диагностика

Если поведение кэширования не так, как ожидалось, убедитесь, что ответы кэшируются и могут обрабатываться из кэша. Изучите Входящие заголовки запроса и исходящие заголовки ответа. Включите [ведение журнала](xref:fundamentals/logging/index) для помощи при отладке.

При тестировании и устранении неполадок кэширования браузер может задавать заголовки запросов, которые влияют на кэширование нежелательным образом. Например, браузер может задать `Cache-Control` для заголовка `no-cache` или `max-age=0` при обновлении страницы. Следующие средства могут явно задавать заголовки запроса и являются предпочтительными для тестирования кэширования.

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Условия для кэширования

* Запрос должен привести к отклику сервера с кодом состояния 200 (ОК).
* Метод запроса должен быть GET или HEAD.
* В `Startup.Configure` по промежуточного слоя, для которого требуется кэширование, должно быть помещено в состояние кэширования ответа. Для получения дополнительной информации см. <xref:fundamentals/middleware/index>.
* `Authorization`Заголовок не должен присутствовать.
* `Cache-Control` параметры заголовка должны быть допустимыми, и ответ должен быть помечен `public` и не помечен `private` .
* `Pragma: no-cache`Заголовок не должен присутствовать `Cache-Control` , если заголовок отсутствует, так как `Cache-Control` заголовок переопределяет `Pragma` заголовок при его наличии.
* `Set-Cookie`Заголовок не должен присутствовать.
* `Vary` параметры заголовка должны быть допустимыми и не равны `*` .
* `Content-Length`Значение заголовка (если оно задано) должно соответствовать размеру текста ответа.
* Объект <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> не используется.
* Ответ не должен быть устаревшим, как указано в `Expires` заголовках и `max-age` `s-maxage` директивах кэша и.
* Буферизация ответов должна быть успешной. Размер ответа должен быть меньше заданного значения или по умолчанию <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Размер текста ответа должен быть меньше заданного значения или по умолчанию <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Ответ должен быть кэширован согласно спецификациям [RFC 7234](https://tools.ietf.org/html/rfc7234) . Например, `no-store` директива не должна существовать в полях заголовка запроса или ответа. Дополнительные сведения см. *в разделе 3. хранение ответов в кэшах* [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Система защиты от подделки для создания безопасных маркеров для предотвращения подделки межсайтовых запросов (CSRF) устанавливает `Cache-Control` заголовки и `Pragma` `no-cache` так, чтобы ответы не кэшируются. Сведения об отключении маркеров подделки для элементов HTML-форм см. в разделе <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end