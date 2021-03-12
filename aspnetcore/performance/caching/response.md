---
title: Кэширование ответов в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать кэширование ответов, чтобы снизить требования к пропускной способности и повысить производительность приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 539ddb118279adb3a53394cdb0c2e5169092ebc0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589235"
---
# <a name="response-caching-in-aspnet-core"></a>Кэширование ответов в ASP.NET Core

[Джон Луо](https://github.com/JunTaoLuo), [Рик Андерсон (](https://twitter.com/RickAndMSFT)и [Виктор Смит](https://ardalis.com/)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/response/samples) ([как скачивать](xref:index#how-to-download-a-sample))

Кэширование ответов сокращает количество запросов к веб-серверу, которые клиент или прокси выполняет. Кэширование ответов также сокращает объем работы, выполняемой веб-сервером для создания ответа. Кэширование ответов управляется заголовками, которые определяют, как клиент, прокси-сервер и промежуточное по должны кэшировать ответы.

[Атрибут респонсекаче](#responsecache-attribute) участвует в установках заголовков кэширования ответа. Клиенты и промежуточные прокси-серверы должны учитывать заголовки для кэширования ответов в [спецификации кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234).

Для кэширования на стороне сервера, которое следует за спецификацией кэширования HTTP 1,1, используйте по [промежуточного слоя кэширование ответа](xref:performance/caching/middleware). По промежуточного слоя может использовать <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> свойства, чтобы повлиять на поведение кэширования на стороне сервера.

## <a name="http-based-response-caching"></a>Кэширование ответов на основе HTTP

[Спецификация кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234) описывает, как должны вести себя Интернет-кэши. Основной заголовок HTTP, используемый для кэширования, — это [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), который используется для указания *директив* кэша. Директивы управляют поведением кэширования в качестве запросов от клиентов к серверам, а также по мере того, как отклики от серверов возвращаются к клиентам. Запросы и ответы перемещаются через прокси-серверы, а прокси-серверы также должны соответствовать спецификации кэширования HTTP 1,1.

Общие `Cache-Control` директивы показаны в следующей таблице.

| Директива                                                       | Действие |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Ответ может храниться в кэше. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Ответ не должен храниться в общем кэше. Частный кэш может хранить и повторно использовать ответ. |
| [максимальный возраст](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Клиент не принимает ответ, возраст которого превышает указанное число секунд. Примеры: `max-age=60` (60 секунд), `max-age=2592000` (1 месяц) |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **В запросах**: кэш не должен использовать сохраненный ответ для удовлетворения запроса. Сервер источника повторно создает ответ для клиента, и по промежуточного слоя обновляет сохраненный ответ в своем кэше.<br><br>**В ответах**: ответ не должен использоваться для последующего запроса без проверки на сервере-источнике. |
| [без магазина](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **В запросах**: кэш не должен сохранять запрос.<br><br>**В** ответах: кэш не должен хранить какую-либо часть ответа. |

Другие заголовки кэша, которые играют роль в кэшировании, показаны в следующей таблице.

| Заголовок                                                     | Функция |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | Оценка количества времени в секундах с момента создания или успешной проверки ответа на сервере источника. |
| [Истекает](https://tools.ietf.org/html/rfc7234#section-5.3) | Время, после которого ответ считается устаревшим. |
| [Включают](https://tools.ietf.org/html/rfc7234#section-5.4)  | Существует для обеспечения обратной совместимости с кэшами HTTP/1.0 для настройки `no-cache` поведения. Если `Cache-Control` заголовок имеется, `Pragma` заголовок игнорируется. |
| [Меняющие](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Указывает, что кэшированный ответ не должен отправляться, если все `Vary` поля заголовка не совпадают как в исходном запросе, так и в новом запросе кэшированного ответа. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Кэширование на основе HTTP учитывает директивы Cache-Control запросов

[Спецификация кэширования HTTP 1,1 для заголовка Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) требует, чтобы кэш соработал с допустимым `Cache-Control` заголовком, отправленным клиентом. Клиент может выполнять запросы со `no-cache` значением заголовка и принудительно выдавать серверу запрос на создание нового ответа для каждого запроса.

Всегда учитывать `Cache-Control` заголовки запросов клиентов имеет смысл, если рассматривать цель кэширования HTTP. В официальной спецификации кэширование предназначено для уменьшения задержки и нагрузки на сети для удовлетворения запросов в сети клиентов, прокси и серверов. Это не обязательно способ управления нагрузкой на сервере-источнике.

При использовании по [промежуточного слоя для кэширования ответа](xref:performance/caching/middleware) разработчик не управляет этим поведением кэширования, поскольку по промежуточного слоя соответствует официальной спецификации кэширования. [Запланированные улучшения по промежуточного слоя](https://github.com/dotnet/AspNetCore/issues/2612) позволяют настроить по промежуточного слоя на игнорирование заголовка запроса `Cache-Control` при принятии решения о выполнении кэшированного ответа. Запланированные улучшения дают возможность лучше управлять нагрузкой сервера.

## <a name="other-caching-technology-in-aspnet-core"></a>Другая технология кэширования в ASP.NET Core

### <a name="in-memory-caching"></a>Кэширование в памяти

В процессе кэширования в памяти используется память сервера для хранения кэшированных данных. Этот тип кэширования подходит для одного сервера или нескольких серверов, использующих *закрепленные сеансы*. Прикрепленные сеансы означает, что запросы от клиента всегда направляются на один и тот же сервер для обработки.

Для получения дополнительной информации см. <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Распределенный кэш

Используйте распределенный кэш для хранения данных в памяти, когда приложение размещается в облаке или ферме серверов. Кэш является общим для серверов, обрабатывающих запросы. Клиент может отправить запрос, который обрабатывается любым сервером в группе, если доступны кэшированные данные для клиента. ASP.NET Core работает с распределенными кэшами SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)и [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Для получения дополнительной информации см. <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Вспомогательная функция тега кэша

Кэширование содержимого из представления MVC или Razor страницы с вспомогательной функцией тега кэша. Вспомогательная функция тега кэша использует кэширование в памяти для хранения данных.

Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Вспомогательная функция тега распределенного кэша

Кэширование содержимого из представления MVC или Razor страницы в сценариях распределенного облака или веб-фермы с помощью вспомогательной функции тега распределенного кэша. Вспомогательная функция тега распределенного кэша использует SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)или [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) для хранения данных.

Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Атрибут Респонсекаче

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Указывает параметры, необходимые для настройки соответствующих заголовков в кэшировании ответов.

> [!WARNING]
> Отключите кэширование для содержимого, содержащего сведения для клиентов, прошедших проверку подлинности. Кэширование следует включать только для содержимого, которое не изменяется в зависимости от удостоверения пользователя или от того, вошел ли пользователь в систему.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> изменяет сохраненный ответ на значения заданного списка ключей запроса. Если указано одно значение `*` , по промежуточного слоя изменяет ответы всеми параметрами строки запроса.

Для установки свойства необходимо включить по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware) <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . В противном случае выдается исключение времени выполнения. Отсутствует соответствующий заголовок HTTP для <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Свойства. Свойство является компонентом HTTP, обрабатываемым по промежуточного слоя кэширования ответа. Чтобы по промежуточного слоя обслуживать кэшированный ответ, строка запроса и значение строки запроса должны соответствовать предыдущему запросу. Например, рассмотрим последовательность запросов и результатов, показанных в следующей таблице.

| Запрос                          | Результат                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Возвращается с сервера. |
| `http://example.com?key1=value1` | Возвращается из промежуточного слоя. |
| `http://example.com?key1=value2` | Возвращается с сервера. |

Первый запрос возвращается сервером и кэшируется по промежуточного слоя. Второй запрос возвращается по промежуточного слоя, так как строка запроса совпадает с предыдущим запросом. Третий запрос не находится в кэше по промежуточного слоя, так как значение строки запроса не соответствует предыдущему запросу.

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Используется для настройки и создания (через <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` . `ResponseCacheFilter`Компонент выполняет работу по обновлению соответствующих HTTP-заголовков и функций ответа. Фильтр:

* Удаляет все существующие заголовки для `Vary` , `Cache-Control` и `Pragma` .
* Записывает соответствующие заголовки на основе свойств, заданных в <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .
* Обновляет компонент кэширования ответов HTTP, если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> задано значение.

### <a name="vary"></a>Меняющие

Этот заголовок записывается только в том случае, если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> задано свойство. Свойство, для которого задано `Vary` значение свойства. В следующем примере используется <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> свойство:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

С помощью примера приложения просмотрите заголовки ответа с помощью сетевых средств браузера. Следующие заголовки ответа отправляются с ответом страницы Cache1:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>\ Store и Location. None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> переопределяет большинство других свойств. Если для этого свойства задано значение `true` , `Cache-Control` заголовок устанавливается в значение `no-store` . Если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> параметр имеет значение `None` :

* Параметру `Cache-Control` задается значение `no-store,no-cache`.
* Параметру `Pragma` задается значение `no-cache`.

Если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> параметр имеет значение `false` <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> , а имеет значение, и, то `None` `Cache-Control` `Pragma` для свойства задаются значения `no-cache` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> обычно имеет значение `true` для страниц ошибок. Страница Cache2 в примере приложения создает заголовки ответа, указывающие клиенту не сохранять ответ.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Пример приложения возвращает страницу Cache2 со следующими заголовками:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Расположение и длительность

Чтобы включить кэширование, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> необходимо задать положительное значение, которое <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> должно быть либо `Any` (по умолчанию), либо `Client` . Платформа задает `Cache-Control` для заголовка значение Location, за которым следует `max-age` ответ.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>параметры `Any` и `Client` транслируются в `Cache-Control` значения заголовков `public` и `private` соответственно. Как указано в разделе "не [Store" и "Location. None](#nostore-and-locationnone) ", параметр <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None` задает для обоих `Cache-Control` `Pragma` заголовков и значение `no-cache` .

`Location.Any` ( `Cache-Control` значение `public` ) указывает, что *клиент или любой промежуточный прокси-сервер* может кэшировать значение, включая по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware).

`Location.Client` ( `Cache-Control` значение `private` ) указывает, что *только клиент* может кэшировать значение. Ни один промежуточный кэш не должен кэшировать значение, включая по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware).

Заголовки управления кэшем просто предоставляют рекомендации клиентам и промежуточным прокси-серверам, когда и как кэшировать ответы. Нет никакой гарантии, что клиенты и прокси-серверы будут учитывать [спецификацию кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234). [Промежуточное расположение кэширования ответов](xref:performance/caching/middleware) всегда соответствует правилам кэширования, указанным в спецификации.

В следующем примере показана модель страницы Cache3 из примера приложения и заголовки, созданные с помощью параметра <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> и значения по умолчанию <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> :

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Пример приложения возвращает страницу Cache3 со следующим заголовком:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Профили кэша

Вместо дублирования параметров кэша ответов во многих атрибутах действия контроллера, профили кэша можно настроить в качестве параметров при настройке MVC и Razor страниц в `Startup.ConfigureServices` . Значения, найденные в указанном профиле кэша, используются по умолчанию <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> и переопределяются любыми свойствами, заданными в атрибуте.

Настройка профиля кэша. В следующем примере показан 30-секундный профиль кэша в примере приложения `Startup.ConfigureServices` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

Модель страницы Cache4 в примере приложения ссылается на `Default30` профиль кэша:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Можно применить к:

* Razor Pages: атрибуты не могут применяться к методам обработчика.
* Контроллеры MVC.
* Методы действий MVC. атрибуты уровня метода переопределяют параметры, указанные в атрибутах уровня класса.

Результирующий заголовок, примененный к ответу страницы Cache4 в `Default30` профиле кэша:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Хранение ответов в кэшах](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control;](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
