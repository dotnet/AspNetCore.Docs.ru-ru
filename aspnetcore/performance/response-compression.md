---
title: Сжатие ответов в ASP.NET Core
author: rick-anderson
description: Сведения о сжатии откликов и способах использования ПО промежуточного слоя для сжатия откликов в приложениях ASP.NET Core.
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
uid: performance/response-compression
ms.openlocfilehash: 239f9e84d068bfd75c84ccf16f0e74cdbbbebfb2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586323"
---
# <a name="response-compression-in-aspnet-core"></a>Сжатие ответов в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Пропускная способность сети является ограниченным ресурсом. Уменьшение размера ответа обычно значительно увеличивает скорость реагирования приложения. Одним из способов уменьшения размера полезных данных является сжатие ответов приложения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>Использование по промежуточного слоя сжатия ответа

Используйте технологии сжатия серверных ответов на базе IIS, Apache или nginx. Возможно, производительность по промежуточного слоя не соответствует числу серверных модулей. [HTTP.sys](xref:fundamentals/servers/httpsys) Server Server и [Kestrel](xref:fundamentals/servers/kestrel) Server в настоящее время не предлагают встроенную поддержку сжатия.

Используйте по промежуточного слоя сжатия ответов, если:

* Не удается использовать следующие технологии сжатия на основе серверов:
  * [Модуль динамического сжатия IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Модуль Apache mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Сжатие и распаковка nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Размещение непосредственно в:
  * [ СерверHTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался «прослушиваемый»)
  * [Сервер Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Сжатие ответов

Как правило, любой ответ, не сжатый в собственном формате, может выиграть от сжатия ответа. Ответы, не сжатые в собственном формате, обычно включают в себя CSS, JavaScript, HTML, XML и JSON. Не следует сжимать в собственном формате активы, такие как PNG-файлы. При попытке дальнейшего сжатия отклика, сжатого в собственном формате, любое небольшое уменьшение размера и времени передачи, скорее всего, будет превышено на время, затраченное на обработку сжатия. Не сжимать файлы размером менее 150-1000 байт (в зависимости от содержимого файла и эффективности сжатия). Затраты на сжатие мелких файлов могут привести к созданию сжатого файла, большего, чем несжатый файл.

Когда клиент может обработать сжатое содержимое, клиент должен сообщить серверу о своих возможностях, отправив `Accept-Encoding` заголовок с запросом. Когда сервер отправляет сжатое содержимое, он должен содержать сведения в `Content-Encoding` заголовке процесса кодирования сжатого ответа. В следующей таблице показаны конструкции кодирования содержимого, поддерживаемые по промежуточного слоя.

| `Accept-Encoding` значения заголовка | Поддерживается по промежуточного слоя | Описание |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Да (по умолчанию)        | [Формат сжатых данных Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Нет                   | [Сжатый формат сжатых данных](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Нет                   | [Эффективный XML-обмен в формате W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Да                  | [Формат файла gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Да                  | Идентификатор "без кодирования": ответ не должен быть закодирован. |
| `pack200-gzip`                  | Нет                   | [Формат сетевой пересылки для архивов Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Да                  | Любая доступная кодировка содержимого, которая не запрашивается явно |

Дополнительные сведения см. в [списке официального кодирования содержимого IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

По промежуточного слоя позволяет добавлять дополнительные поставщики сжатия для `Accept-Encoding` значений пользовательских заголовков. Дополнительные сведения см. в разделе [Настраиваемые поставщики](#custom-providers) ниже.

По промежуточного слоя может отреагировать на `q` весовые коэффициенты качества (квалуе), которые отправляются клиентом для определения приоритета схем сжатия. Дополнительные сведения см. в [документе RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Алгоритмы сжатия подчиняются компромиссу между скоростью сжатия и эффективностью сжатия. *Эффективность* в этом контексте означает размер выходных данных после сжатия. Наименьший размер достигается самым *оптимальным* сжатием.

Заголовки, используемые для запроса, отправки, кэширования и получения сжатого содержимого, описаны в таблице ниже.

| Заголовок             | Роль |
| ------------------ | ---- |
| `Accept-Encoding`  | Отправляется с клиента на сервер, чтобы указать схемы кодировки содержимого, приемлемые для клиента. |
| `Content-Encoding` | Отправляется с сервера клиенту для указания кодировки содержимого в полезных данных. |
| `Content-Length`   | Когда происходит сжатие, `Content-Length` заголовок удаляется, так как содержимое текста изменяется при сжатии ответа. |
| `Content-MD5`      | Когда происходит сжатие, `Content-MD5` заголовок удаляется, так как содержимое текста изменилось и хэш больше не является допустимым. |
| `Content-Type`     | Указывает тип MIME содержимого. Каждый ответ должен указывать его `Content-Type` . По промежуточного слоя проверяет это значение, чтобы определить, следует ли сжимать ответ. По промежуточного слоя указывает набор [типов MIME по умолчанию](#mime-types) , которые он может кодировать, но можно заменить или добавить типы MIME. |
| `Vary`             | При отправке сервером значения `Accept-Encoding` для клиентов и прокси `Vary` Заголовок указывает клиенту или прокси-серверу, что он должен кэшировать (варьировать) ответы в зависимости от значения `Accept-Encoding` заголовка запроса. Результат возврата содержимого с `Vary: Accept-Encoding` заголовком заключается в том, что как сжатые, так и несжатые ответы кэшируются отдельно. |

Изучите функции по промежуточного слоя сжатия ответов с [примером приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples). В примере показано следующее:

* Сжатие ответов приложений с помощью gzip и пользовательских поставщиков сжатия.
* Добавление типа MIME в список по умолчанию типов MIME для сжатия.

## <a name="package"></a>Пакет

По промежуточного слоя сжатия ответа предоставляется пакетом [Microsoft. AspNetCore. респонсекомпрессион](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) , который неявно включается в ASP.NET Core приложения.

## <a name="configuration"></a>Конфигурация

В следующем коде показано, как включить по промежуточного слоя сжатия ответа для типов MIME по умолчанию и поставщиков сжатия ([Brotli](#brotli-compression-provider) и [gzip](#gzip-compression-provider)):

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Примечания.

* `app.UseResponseCompression` должен вызываться до любого по промежуточного слоя, которое сжимает ответы. Для получения дополнительной информации см. <xref:fundamentals/middleware/index#middleware-order>.
* Используйте такое средство, как [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)или [POST](https://www.getpostman.com/) , чтобы задать `Accept-Encoding` заголовок запроса и изучить заголовки, размер и текст ответа.

Отправьте запрос в пример приложения без `Accept-Encoding` заголовка и обратите внимание, что ответ не сжат. `Content-Encoding`Заголовки и `Vary` отсутствуют в ответе.

![Окно Fiddler, показывающее результат запроса без заголовка Accept-Encoding. Ответ не сжат.](response-compression/_static/request-uncompressed.png)

Отправьте запрос в пример приложения с `Accept-Encoding: br` заголовком (Brotli Compression) и обратите внимание на то, что ответ сжат. В `Content-Encoding` `Vary` ответе имеются заголовки и.

![Окно Fiddler, показывающее результат запроса с заголовком Accept-Encoding и значением br. Заголовки Vary и Content-Encoding добавляются в ответ. Ответ сжимается.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>Поставщики

### <a name="brotli-compression-provider"></a>Поставщик сжатия Brotli

Используйте <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> для сжатия ответов с [форматом сжатых данных Brotli](https://tools.ietf.org/html/rfc7932).

Если поставщики сжатия явно не добавляются в <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Поставщик сжатия Brotli по умолчанию добавляется в массив поставщиков сжатия вместе с [поставщиком сжатия Gzip](#gzip-compression-provider).
* Сжатие по умолчанию используется для сжатия Brotli, если клиент поддерживает формат сжатых данных Brotli. Если Brotli не поддерживается клиентом, то сжатие по умолчанию используется gzip, если клиент поддерживает сжатие Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Поставщик сжатия Brotli должен быть добавлен при явном добавлении любых поставщиков сжатия:

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

Задайте уровень сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> . Поставщик сжатия Brotli по умолчанию имеет самый быстрый уровень сжатия ([CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel)), что может привести к неэффективному сжатию. Если требуется наиболее эффективное сжатие, настройте по промежуточного слоя для оптимального сжатия.

| Compression Level | Описание |
| ----------------- | ----------- |
| [CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel) | Сжатие должно завершаться как можно быстрее, даже если полученные выходные данные не будут оптимально сжаты. |
| [CompressionLevel. уплотнение](xref:System.IO.Compression.CompressionLevel) | Сжатие выполнять не нужно. |
| [CompressionLevel. оптимально](xref:System.IO.Compression.CompressionLevel) | Ответы должны быть оптимально сжаты, даже если сжатие занимает больше времени. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a>Поставщик сжатия GZIP

Используйте <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> для сжатия ответов с [форматом файла gzip](https://tools.ietf.org/html/rfc1952).

Если поставщики сжатия явно не добавляются в <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Поставщик сжатия Gzip добавляется по умолчанию в массив поставщиков сжатия вместе с [поставщиком сжатия Brotli](#brotli-compression-provider).
* Сжатие по умолчанию используется для сжатия Brotli, если клиент поддерживает формат сжатых данных Brotli. Если Brotli не поддерживается клиентом, то сжатие по умолчанию используется gzip, если клиент поддерживает сжатие Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

При явном добавлении поставщиков сжатия необходимо добавить поставщик сжатия gzip:

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

Задайте уровень сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> . Поставщик сжатия Gzip по умолчанию имеет самый быстрый уровень сжатия ([CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel)), что может не привести к максимально эффективному сжатию. Если требуется наиболее эффективное сжатие, настройте по промежуточного слоя для оптимального сжатия.

| Compression Level | Описание |
| ----------------- | ----------- |
| [CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel) | Сжатие должно завершаться как можно быстрее, даже если полученные выходные данные не будут оптимально сжаты. |
| [CompressionLevel. уплотнение](xref:System.IO.Compression.CompressionLevel) | Сжатие выполнять не нужно. |
| [CompressionLevel. оптимально](xref:System.IO.Compression.CompressionLevel) | Ответы должны быть оптимально сжаты, даже если сжатие занимает больше времени. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Настраиваемые поставщики

Создание пользовательских реализаций сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> . <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>Представляет кодировку содержимого, которую создает этот объект `ICompressionProvider` . По промежуточного слоя использует эти сведения для выбора поставщика на основе списка, указанного в `Accept-Encoding` заголовке запроса.

С помощью примера приложения клиент отправляет запрос с `Accept-Encoding: mycustomcompression` заголовком. По промежуточного слоя использует реализацию пользовательского сжатия и возвращает ответ с `Content-Encoding: mycustomcompression` заголовком. Клиент должен иметь возможность распаковать пользовательскую кодировку, чтобы обеспечить работу пользовательской реализации сжатия.

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]


Отправьте запрос в пример приложения с `Accept-Encoding: mycustomcompression` заголовком и просмотрите заголовки ответа. В `Vary` `Content-Encoding` ответе имеются заголовки и. Текст ответа (не показан) не сжимается образцом. В классе примера нет реализации сжатия `CustomCompressionProvider` . Однако в примере показано, где следует реализовать такой алгоритм сжатия.

![Окно Fiddler, показывающее результат запроса с заголовком Accept-Encoding и значением микустомкомпрессион. Заголовки Vary и Content-Encoding добавляются в ответ.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>типы MIME

По промежуточного слоя указывает набор типов MIME по умолчанию для сжатия:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Замените или добавьте типы MIME с помощью параметров по промежуточного слоя сжатия ответа. Обратите внимание, что типы MIME с подстановочными знаками, такие как, `text/*` не поддерживаются. Пример приложения добавляет тип MIME для `image/svg+xml` и сжимает и обслуживает изображение ASP.NET Core баннера (*Banner. SVG*).

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>Сжатие по защищенному протоколу

Сжатые ответы по защищенным подключениям можно контролировать с помощью `EnableForHttps` параметра, который по умолчанию отключен. Использование сжатия с динамически создаваемыми страницами может привести к проблемам безопасности, таким как [преступления](https://wikipedia.org/wiki/CRIME_(security_exploit)) и [нарушение](https://wikipedia.org/wiki/BREACH_(security_exploit)) атак.

## <a name="adding-the-vary-header"></a>Добавление заголовка Vary

При сжатии ответов на основе `Accept-Encoding` заголовка существует потенциально несколько сжатых версий ответа и несжатая версия. Чтобы настроить кэш клиента и прокси-сервера на наличие нескольких версий и их хранения, `Vary` заголовок добавляется со `Accept-Encoding` значением. В ASP.NET Core 2,0 или более поздней версии по промежуточного слоя добавляет `Vary` Заголовок автоматически при сжатии ответа.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Проблемы по промежуточного слоя, когда nginx обратный прокси-сервер

При выполнении запроса через прокси-сервер nginx `Accept-Encoding` заголовок удаляется. Удаление `Accept-Encoding` заголовка предотвращает сжатие ответа по промежуточного слоя. Дополнительные сведения см. в разделе [nginx: сжатие и распаковка](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Эта проблема проявляется на [рисунке сквозного сжатия для nginx (ASPNET/басикмиддлеваре #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Работа с динамическим сжатием IIS

При наличии активного модуля динамического сжатия IIS, настроенного на уровне сервера, который вы хотите отключить для приложения, отключите модуль, дополнив добавление к файлу *web.config* . Дополнительные сведения см. в разделе [Отключение модулей IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Диагностика

Используйте такие средства, как [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)или [POST](https://www.getpostman.com/), которые позволяют задать `Accept-Encoding` заголовок запроса и изучить заголовки, размер и текст ответа. По умолчанию по промежуточного слоя для сжатия ответов сжимает ответы, соответствующие следующим условиям.

* `Accept-Encoding`Заголовок представлен со значением `br` ,, `gzip` `*` или настраиваемой кодировкой, соответствующей настраиваемому поставщику сжатия, который вы установили. Значение не должно быть `identity` или иметь значение свойства (квалуе,), равное `q` 0 (нулю).
* Тип MIME ( `Content-Type` ) должен быть установлен и должен соответствовать типу MIME, настроенному в <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .
* Запрос не должен включать `Content-Range` заголовок.
* В запросе должен использоваться небезопасный протокол (http), если в параметрах по промежуточного слоя сжатия отклика не настроен защищенный протокол (HTTPS). *Обратите внимание на опасность, [описанную выше](#compression-with-secure-protocol) , при включении безопасного сжатия содержимого.*

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Сеть для разработчиков Mozilla: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Раздел RFC 7231 3.1.2.1. Кодирование содержимого](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [Раздел RFC 7230 4.2.3: кодирование gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Спецификация формата файла GZIP, версия 4,3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Пропускная способность сети является ограниченным ресурсом. Уменьшение размера ответа обычно значительно увеличивает скорость реагирования приложения. Одним из способов уменьшения размера полезных данных является сжатие ответов приложения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>Использование по промежуточного слоя сжатия ответа

Используйте технологии сжатия серверных ответов на базе IIS, Apache или nginx. Возможно, производительность по промежуточного слоя не соответствует числу серверных модулей. [HTTP.sys](xref:fundamentals/servers/httpsys) Server Server и [Kestrel](xref:fundamentals/servers/kestrel) Server в настоящее время не предлагают встроенную поддержку сжатия.

Используйте по промежуточного слоя сжатия ответов, если:

* Не удается использовать следующие технологии сжатия на основе серверов:
  * [Модуль динамического сжатия IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Модуль Apache mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Сжатие и распаковка nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Размещение непосредственно в:
  * [ СерверHTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался «прослушиваемый»)
  * [Сервер Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Сжатие ответов

Как правило, любой ответ, не сжатый в собственном формате, может выиграть от сжатия ответа. Ответы, не сжатые в собственном формате, обычно включают в себя CSS, JavaScript, HTML, XML и JSON. Не следует сжимать в собственном формате активы, такие как PNG-файлы. При попытке дальнейшего сжатия отклика, сжатого в собственном формате, любое небольшое уменьшение размера и времени передачи, скорее всего, будет превышено на время, затраченное на обработку сжатия. Не сжимать файлы размером менее 150-1000 байт (в зависимости от содержимого файла и эффективности сжатия). Затраты на сжатие мелких файлов могут привести к созданию сжатого файла, большего, чем несжатый файл.

Когда клиент может обработать сжатое содержимое, клиент должен сообщить серверу о своих возможностях, отправив `Accept-Encoding` заголовок с запросом. Когда сервер отправляет сжатое содержимое, он должен содержать сведения в `Content-Encoding` заголовке процесса кодирования сжатого ответа. В следующей таблице показаны конструкции кодирования содержимого, поддерживаемые по промежуточного слоя.

| `Accept-Encoding` значения заголовка | Поддерживается по промежуточного слоя | Описание |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Да (по умолчанию)        | [Формат сжатых данных Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Нет                   | [Сжатый формат сжатых данных](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Нет                   | [Эффективный XML-обмен в формате W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Да                  | [Формат файла gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Да                  | Идентификатор "без кодирования": ответ не должен быть закодирован. |
| `pack200-gzip`                  | Нет                   | [Формат сетевой пересылки для архивов Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Да                  | Любая доступная кодировка содержимого, которая не запрашивается явно |

Дополнительные сведения см. в [списке официального кодирования содержимого IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

По промежуточного слоя позволяет добавлять дополнительные поставщики сжатия для `Accept-Encoding` значений пользовательских заголовков. Дополнительные сведения см. в разделе [Настраиваемые поставщики](#custom-providers) ниже.

По промежуточного слоя может отреагировать на `q` весовые коэффициенты качества (квалуе), которые отправляются клиентом для определения приоритета схем сжатия. Дополнительные сведения см. в [документе RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Алгоритмы сжатия подчиняются компромиссу между скоростью сжатия и эффективностью сжатия. *Эффективность* в этом контексте означает размер выходных данных после сжатия. Наименьший размер достигается самым *оптимальным* сжатием.

Заголовки, используемые для запроса, отправки, кэширования и получения сжатого содержимого, описаны в таблице ниже.

| Заголовок             | Роль |
| ------------------ | ---- |
| `Accept-Encoding`  | Отправляется с клиента на сервер, чтобы указать схемы кодировки содержимого, приемлемые для клиента. |
| `Content-Encoding` | Отправляется с сервера клиенту для указания кодировки содержимого в полезных данных. |
| `Content-Length`   | Когда происходит сжатие, `Content-Length` заголовок удаляется, так как содержимое текста изменяется при сжатии ответа. |
| `Content-MD5`      | Когда происходит сжатие, `Content-MD5` заголовок удаляется, так как содержимое текста изменилось и хэш больше не является допустимым. |
| `Content-Type`     | Указывает тип MIME содержимого. Каждый ответ должен указывать его `Content-Type` . По промежуточного слоя проверяет это значение, чтобы определить, следует ли сжимать ответ. По промежуточного слоя указывает набор [типов MIME по умолчанию](#mime-types) , которые он может кодировать, но можно заменить или добавить типы MIME. |
| `Vary`             | При отправке сервером значения `Accept-Encoding` для клиентов и прокси `Vary` Заголовок указывает клиенту или прокси-серверу, что он должен кэшировать (варьировать) ответы в зависимости от значения `Accept-Encoding` заголовка запроса. Результат возврата содержимого с `Vary: Accept-Encoding` заголовком заключается в том, что как сжатые, так и несжатые ответы кэшируются отдельно. |

Изучите функции по промежуточного слоя сжатия ответов с [примером приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples). В примере показано следующее:

* Сжатие ответов приложений с помощью gzip и пользовательских поставщиков сжатия.
* Добавление типа MIME в список по умолчанию типов MIME для сжатия.

## <a name="package"></a>Пакет

Чтобы включить по промежуточного слоя в проект, добавьте ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app), которая включает пакет [Microsoft. AspNetCore. респонсекомпрессион](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .

## <a name="configuration"></a>Конфигурация

В следующем коде показано, как включить по промежуточного слоя сжатия ответа для типов MIME по умолчанию и поставщиков сжатия ([Brotli](#brotli-compression-provider) и [gzip](#gzip-compression-provider)):

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Примечания.

* `app.UseResponseCompression` должен вызываться до любого по промежуточного слоя, которое сжимает ответы. Для получения дополнительной информации см. <xref:fundamentals/middleware/index#middleware-order>.
* Используйте такое средство, как [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)или [POST](https://www.getpostman.com/) , чтобы задать `Accept-Encoding` заголовок запроса и изучить заголовки, размер и текст ответа.

Отправьте запрос в пример приложения без `Accept-Encoding` заголовка и обратите внимание, что ответ не сжат. `Content-Encoding`Заголовки и `Vary` отсутствуют в ответе.

![Окно Fiddler, показывающее результат запроса без заголовка Accept-Encoding. Ответ не сжат.](response-compression/_static/request-uncompressed.png)

Отправьте запрос в пример приложения с `Accept-Encoding: br` заголовком (Brotli Compression) и обратите внимание на то, что ответ сжат. В `Content-Encoding` `Vary` ответе имеются заголовки и.

![Окно Fiddler, показывающее результат запроса с заголовком Accept-Encoding и значением br. Заголовки Vary и Content-Encoding добавляются в ответ. Ответ сжимается.](response-compression/_static/request-compressed-br.png)

## <a name="providers"></a>Поставщики

### <a name="brotli-compression-provider"></a>Поставщик сжатия Brotli

Используйте <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> для сжатия ответов с [форматом сжатых данных Brotli](https://tools.ietf.org/html/rfc7932).

Если поставщики сжатия явно не добавляются в <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Поставщик сжатия Brotli по умолчанию добавляется в массив поставщиков сжатия вместе с [поставщиком сжатия Gzip](#gzip-compression-provider).
* Сжатие по умолчанию используется для сжатия Brotli, если клиент поддерживает формат сжатых данных Brotli. Если Brotli не поддерживается клиентом, то сжатие по умолчанию используется gzip, если клиент поддерживает сжатие Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Поставщик сжатия Brotli должен быть добавлен при явном добавлении любых поставщиков сжатия:

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

Задайте уровень сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions> . Поставщик сжатия Brotli по умолчанию имеет самый быстрый уровень сжатия ([CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel)), что может привести к неэффективному сжатию. Если требуется наиболее эффективное сжатие, настройте по промежуточного слоя для оптимального сжатия.

| Compression Level | Описание |
| ----------------- | ----------- |
| [CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel) | Сжатие должно завершаться как можно быстрее, даже если полученные выходные данные не будут оптимально сжаты. |
| [CompressionLevel. уплотнение](xref:System.IO.Compression.CompressionLevel) | Сжатие выполнять не нужно. |
| [CompressionLevel. оптимально](xref:System.IO.Compression.CompressionLevel) | Ответы должны быть оптимально сжаты, даже если сжатие занимает больше времени. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="gzip-compression-provider"></a>Поставщик сжатия GZIP

Используйте <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> для сжатия ответов с [форматом файла gzip](https://tools.ietf.org/html/rfc1952).

Если поставщики сжатия явно не добавляются в <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Поставщик сжатия Gzip добавляется по умолчанию в массив поставщиков сжатия вместе с [поставщиком сжатия Brotli](#brotli-compression-provider).
* Сжатие по умолчанию используется для сжатия Brotli, если клиент поддерживает формат сжатых данных Brotli. Если Brotli не поддерживается клиентом, то сжатие по умолчанию используется gzip, если клиент поддерживает сжатие Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

При явном добавлении поставщиков сжатия необходимо добавить поставщик сжатия gzip:

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

Задайте уровень сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> . Поставщик сжатия Gzip по умолчанию имеет самый быстрый уровень сжатия ([CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel)), что может не привести к максимально эффективному сжатию. Если требуется наиболее эффективное сжатие, настройте по промежуточного слоя для оптимального сжатия.

| Compression Level | Описание |
| ----------------- | ----------- |
| [CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel) | Сжатие должно завершаться как можно быстрее, даже если полученные выходные данные не будут оптимально сжаты. |
| [CompressionLevel. уплотнение](xref:System.IO.Compression.CompressionLevel) | Сжатие выполнять не нужно. |
| [CompressionLevel. оптимально](xref:System.IO.Compression.CompressionLevel) | Ответы должны быть оптимально сжаты, даже если сжатие занимает больше времени. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Настраиваемые поставщики

Создание пользовательских реализаций сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> . <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>Представляет кодировку содержимого, которую создает этот объект `ICompressionProvider` . По промежуточного слоя использует эти сведения для выбора поставщика на основе списка, указанного в `Accept-Encoding` заголовке запроса.

С помощью примера приложения клиент отправляет запрос с `Accept-Encoding: mycustomcompression` заголовком. По промежуточного слоя использует реализацию пользовательского сжатия и возвращает ответ с `Content-Encoding: mycustomcompression` заголовком. Клиент должен иметь возможность распаковать пользовательскую кодировку, чтобы обеспечить работу пользовательской реализации сжатия.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

Отправьте запрос в пример приложения с `Accept-Encoding: mycustomcompression` заголовком и просмотрите заголовки ответа. В `Vary` `Content-Encoding` ответе имеются заголовки и. Текст ответа (не показан) не сжимается образцом. В классе примера нет реализации сжатия `CustomCompressionProvider` . Однако в примере показано, где следует реализовать такой алгоритм сжатия.

![Окно Fiddler, показывающее результат запроса с заголовком Accept-Encoding и значением микустомкомпрессион. Заголовки Vary и Content-Encoding добавляются в ответ.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>типы MIME

По промежуточного слоя указывает набор типов MIME по умолчанию для сжатия:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Замените или добавьте типы MIME с помощью параметров по промежуточного слоя сжатия ответа. Обратите внимание, что типы MIME с подстановочными знаками, такие как, `text/*` не поддерживаются. Пример приложения добавляет тип MIME для `image/svg+xml` и сжимает и обслуживает изображение ASP.NET Core баннера (*Banner. SVG*).

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>Сжатие по защищенному протоколу

Сжатые ответы по защищенным подключениям можно контролировать с помощью `EnableForHttps` параметра, который по умолчанию отключен. Использование сжатия с динамически создаваемыми страницами может привести к проблемам безопасности, таким как [преступления](https://wikipedia.org/wiki/CRIME_(security_exploit)) и [нарушение](https://wikipedia.org/wiki/BREACH_(security_exploit)) атак.

## <a name="adding-the-vary-header"></a>Добавление заголовка Vary

При сжатии ответов на основе `Accept-Encoding` заголовка существует потенциально несколько сжатых версий ответа и несжатая версия. Чтобы настроить кэш клиента и прокси-сервера на наличие нескольких версий и их хранения, `Vary` заголовок добавляется со `Accept-Encoding` значением. В ASP.NET Core 2,0 или более поздней версии по промежуточного слоя добавляет `Vary` Заголовок автоматически при сжатии ответа.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Проблемы по промежуточного слоя, когда nginx обратный прокси-сервер

При выполнении запроса через прокси-сервер nginx `Accept-Encoding` заголовок удаляется. Удаление `Accept-Encoding` заголовка предотвращает сжатие ответа по промежуточного слоя. Дополнительные сведения см. в разделе [nginx: сжатие и распаковка](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Эта проблема проявляется на [рисунке сквозного сжатия для nginx (ASPNET/басикмиддлеваре #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Работа с динамическим сжатием IIS

При наличии активного модуля динамического сжатия IIS, настроенного на уровне сервера, который вы хотите отключить для приложения, отключите модуль, дополнив добавление к файлу *web.config* . Дополнительные сведения см. в разделе [Отключение модулей IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Диагностика

Используйте такие средства, как [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)или [POST](https://www.getpostman.com/), которые позволяют задать `Accept-Encoding` заголовок запроса и изучить заголовки, размер и текст ответа. По умолчанию по промежуточного слоя для сжатия ответов сжимает ответы, соответствующие следующим условиям.

* `Accept-Encoding`Заголовок представлен со значением `br` ,, `gzip` `*` или настраиваемой кодировкой, соответствующей настраиваемому поставщику сжатия, который вы установили. Значение не должно быть `identity` или иметь значение свойства (квалуе,), равное `q` 0 (нулю).
* Тип MIME ( `Content-Type` ) должен быть установлен и должен соответствовать типу MIME, настроенному в <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .
* Запрос не должен включать `Content-Range` заголовок.
* В запросе должен использоваться небезопасный протокол (http), если в параметрах по промежуточного слоя сжатия отклика не настроен защищенный протокол (HTTPS). *Обратите внимание на опасность, [описанную выше](#compression-with-secure-protocol) , при включении безопасного сжатия содержимого.*

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Сеть для разработчиков Mozilla: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Раздел RFC 7231 3.1.2.1. Кодирование содержимого](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [Раздел RFC 7230 4.2.3: кодирование gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Спецификация формата файла GZIP, версия 4,3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Пропускная способность сети является ограниченным ресурсом. Уменьшение размера ответа обычно значительно увеличивает скорость реагирования приложения. Одним из способов уменьшения размера полезных данных является сжатие ответов приложения.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="when-to-use-response-compression-middleware"></a>Использование по промежуточного слоя сжатия ответа

Используйте технологии сжатия серверных ответов на базе IIS, Apache или nginx. Возможно, производительность по промежуточного слоя не соответствует числу серверных модулей. [HTTP.sys](xref:fundamentals/servers/httpsys) Server Server и [Kestrel](xref:fundamentals/servers/kestrel) Server в настоящее время не предлагают встроенную поддержку сжатия.

Используйте по промежуточного слоя сжатия ответов, если:

* Не удается использовать следующие технологии сжатия на основе серверов:
  * [Модуль динамического сжатия IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Модуль Apache mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Сжатие и распаковка nginx](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Размещение непосредственно в:
  * [ СерверHTTP.sys](xref:fundamentals/servers/httpsys) (ранее назывался «прослушиваемый»)
  * [Сервер Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Сжатие ответов

Как правило, любой ответ, не сжатый в собственном формате, может выиграть от сжатия ответа. Ответы, не сжатые в собственном формате, обычно включают в себя CSS, JavaScript, HTML, XML и JSON. Не следует сжимать в собственном формате активы, такие как PNG-файлы. При попытке дальнейшего сжатия отклика, сжатого в собственном формате, любое небольшое уменьшение размера и времени передачи, скорее всего, будет превышено на время, затраченное на обработку сжатия. Не сжимать файлы размером менее 150-1000 байт (в зависимости от содержимого файла и эффективности сжатия). Затраты на сжатие мелких файлов могут привести к созданию сжатого файла, большего, чем несжатый файл.

Когда клиент может обработать сжатое содержимое, клиент должен сообщить серверу о своих возможностях, отправив `Accept-Encoding` заголовок с запросом. Когда сервер отправляет сжатое содержимое, он должен содержать сведения в `Content-Encoding` заголовке процесса кодирования сжатого ответа. В следующей таблице показаны конструкции кодирования содержимого, поддерживаемые по промежуточного слоя.

| `Accept-Encoding` значения заголовка | Поддерживается по промежуточного слоя | Описание |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Нет                   | [Формат сжатых данных Brotli](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Нет                   | [Сжатый формат сжатых данных](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Нет                   | [Эффективный XML-обмен в формате W3C](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Да (по умолчанию)        | [Формат файла gzip](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Да                  | Идентификатор "без кодирования": ответ не должен быть закодирован. |
| `pack200-gzip`                  | Нет                   | [Формат сетевой пересылки для архивов Java](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Да                  | Любая доступная кодировка содержимого, которая не запрашивается явно |

Дополнительные сведения см. в [списке официального кодирования содержимого IANA](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

По промежуточного слоя позволяет добавлять дополнительные поставщики сжатия для `Accept-Encoding` значений пользовательских заголовков. Дополнительные сведения см. в разделе [Настраиваемые поставщики](#custom-providers) ниже.

По промежуточного слоя может отреагировать на `q` весовые коэффициенты качества (квалуе), которые отправляются клиентом для определения приоритета схем сжатия. Дополнительные сведения см. в [документе RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Алгоритмы сжатия подчиняются компромиссу между скоростью сжатия и эффективностью сжатия. *Эффективность* в этом контексте означает размер выходных данных после сжатия. Наименьший размер достигается самым *оптимальным* сжатием.

Заголовки, используемые для запроса, отправки, кэширования и получения сжатого содержимого, описаны в таблице ниже.

| Заголовок             | Роль |
| ------------------ | ---- |
| `Accept-Encoding`  | Отправляется с клиента на сервер, чтобы указать схемы кодировки содержимого, приемлемые для клиента. |
| `Content-Encoding` | Отправляется с сервера клиенту для указания кодировки содержимого в полезных данных. |
| `Content-Length`   | Когда происходит сжатие, `Content-Length` заголовок удаляется, так как содержимое текста изменяется при сжатии ответа. |
| `Content-MD5`      | Когда происходит сжатие, `Content-MD5` заголовок удаляется, так как содержимое текста изменилось и хэш больше не является допустимым. |
| `Content-Type`     | Указывает тип MIME содержимого. Каждый ответ должен указывать его `Content-Type` . По промежуточного слоя проверяет это значение, чтобы определить, следует ли сжимать ответ. По промежуточного слоя указывает набор [типов MIME по умолчанию](#mime-types) , которые он может кодировать, но можно заменить или добавить типы MIME. |
| `Vary`             | При отправке сервером значения `Accept-Encoding` для клиентов и прокси `Vary` Заголовок указывает клиенту или прокси-серверу, что он должен кэшировать (варьировать) ответы в зависимости от значения `Accept-Encoding` заголовка запроса. Результат возврата содержимого с `Vary: Accept-Encoding` заголовком заключается в том, что как сжатые, так и несжатые ответы кэшируются отдельно. |

Изучите функции по промежуточного слоя сжатия ответов с [примером приложения](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples). В примере показано следующее:

* Сжатие ответов приложений с помощью gzip и пользовательских поставщиков сжатия.
* Добавление типа MIME в список по умолчанию типов MIME для сжатия.

## <a name="package"></a>Пакет

Чтобы включить по промежуточного слоя в проект, добавьте ссылку на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app), которая включает пакет [Microsoft. AspNetCore. респонсекомпрессион](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) .

## <a name="configuration"></a>Конфигурация

В следующем коде показано, как включить по промежуточного слоя сжатия ответов для типов MIME по умолчанию и [поставщика сжатия Gzip](#gzip-compression-provider):

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Примечания.

* `app.UseResponseCompression` должен вызываться до любого по промежуточного слоя, которое сжимает ответы. Для получения дополнительной информации см. <xref:fundamentals/middleware/index#middleware-order>.
* Используйте такое средство, как [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)или [POST](https://www.getpostman.com/) , чтобы задать `Accept-Encoding` заголовок запроса и изучить заголовки, размер и текст ответа.

Отправьте запрос в пример приложения без `Accept-Encoding` заголовка и обратите внимание, что ответ не сжат. `Content-Encoding`Заголовки и `Vary` отсутствуют в ответе.

![Окно Fiddler, показывающее результат запроса без заголовка Accept-Encoding. Ответ не сжат.](response-compression/_static/request-uncompressed.png)

Отправьте запрос в пример приложения с `Accept-Encoding: gzip` заголовком и убедитесь, что ответ сжат. В `Content-Encoding` `Vary` ответе имеются заголовки и.

![Окно Fiddler, показывающее результат запроса с заголовком Accept-Encoding и значением gzip. Заголовки Vary и Content-Encoding добавляются в ответ. Ответ сжимается.](response-compression/_static/request-compressed.png)

## <a name="providers"></a>Поставщики

### <a name="gzip-compression-provider"></a>Поставщик сжатия GZIP

Используйте <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> для сжатия ответов с [форматом файла gzip](https://tools.ietf.org/html/rfc1952).

Если поставщики сжатия явно не добавляются в <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection> :

* Поставщик сжатия Gzip добавляется по умолчанию в массив поставщиков сжатия.
* Сжатие по умолчанию используется gzip, если клиент поддерживает сжатие Gzip.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

При явном добавлении поставщиков сжатия необходимо добавить поставщик сжатия gzip:

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

Задайте уровень сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions> . Поставщик сжатия Gzip по умолчанию имеет самый быстрый уровень сжатия ([CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel)), что может не привести к максимально эффективному сжатию. Если требуется наиболее эффективное сжатие, настройте по промежуточного слоя для оптимального сжатия.

| Compression Level | Описание |
| ----------------- | ----------- |
| [CompressionLevel. самый быстрый](xref:System.IO.Compression.CompressionLevel) | Сжатие должно завершаться как можно быстрее, даже если полученные выходные данные не будут оптимально сжаты. |
| [CompressionLevel. уплотнение](xref:System.IO.Compression.CompressionLevel) | Сжатие выполнять не нужно. |
| [CompressionLevel. оптимально](xref:System.IO.Compression.CompressionLevel) | Ответы должны быть оптимально сжаты, даже если сжатие занимает больше времени. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Настраиваемые поставщики

Создание пользовательских реализаций сжатия с помощью <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider> . <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*>Представляет кодировку содержимого, которую создает этот объект `ICompressionProvider` . По промежуточного слоя использует эти сведения для выбора поставщика на основе списка, указанного в `Accept-Encoding` заголовке запроса.

С помощью примера приложения клиент отправляет запрос с `Accept-Encoding: mycustomcompression` заголовком. По промежуточного слоя использует реализацию пользовательского сжатия и возвращает ответ с `Content-Encoding: mycustomcompression` заголовком. Клиент должен иметь возможность распаковать пользовательскую кодировку, чтобы обеспечить работу пользовательской реализации сжатия.

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

Отправьте запрос в пример приложения с `Accept-Encoding: mycustomcompression` заголовком и просмотрите заголовки ответа. В `Vary` `Content-Encoding` ответе имеются заголовки и. Текст ответа (не показан) не сжимается образцом. В классе примера нет реализации сжатия `CustomCompressionProvider` . Однако в примере показано, где следует реализовать такой алгоритм сжатия.

![Окно Fiddler, показывающее результат запроса с заголовком Accept-Encoding и значением микустомкомпрессион. Заголовки Vary и Content-Encoding добавляются в ответ.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>типы MIME

По промежуточного слоя указывает набор типов MIME по умолчанию для сжатия:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Замените или добавьте типы MIME с помощью параметров по промежуточного слоя сжатия ответа. Обратите внимание, что типы MIME с подстановочными знаками, такие как, `text/*` не поддерживаются. Пример приложения добавляет тип MIME для `image/svg+xml` и сжимает и обслуживает изображение ASP.NET Core баннера (*Banner. SVG*).

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

## <a name="compression-with-secure-protocol"></a>Сжатие по защищенному протоколу

Сжатые ответы по защищенным подключениям можно контролировать с помощью `EnableForHttps` параметра, который по умолчанию отключен. Использование сжатия с динамически создаваемыми страницами может привести к проблемам безопасности, таким как [преступления](https://wikipedia.org/wiki/CRIME_(security_exploit)) и [нарушение](https://wikipedia.org/wiki/BREACH_(security_exploit)) атак.

## <a name="adding-the-vary-header"></a>Добавление заголовка Vary

При сжатии ответов на основе `Accept-Encoding` заголовка существует потенциально несколько сжатых версий ответа и несжатая версия. Чтобы настроить кэш клиента и прокси-сервера на наличие нескольких версий и их хранения, `Vary` заголовок добавляется со `Accept-Encoding` значением. В ASP.NET Core 2,0 или более поздней версии по промежуточного слоя добавляет `Vary` Заголовок автоматически при сжатии ответа.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Проблемы по промежуточного слоя, когда nginx обратный прокси-сервер

При выполнении запроса через прокси-сервер nginx `Accept-Encoding` заголовок удаляется. Удаление `Accept-Encoding` заголовка предотвращает сжатие ответа по промежуточного слоя. Дополнительные сведения см. в разделе [nginx: сжатие и распаковка](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Эта проблема проявляется на [рисунке сквозного сжатия для nginx (ASPNET/басикмиддлеваре #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Работа с динамическим сжатием IIS

При наличии активного модуля динамического сжатия IIS, настроенного на уровне сервера, который вы хотите отключить для приложения, отключите модуль, дополнив добавление к файлу *web.config* . Дополнительные сведения см. в разделе [Отключение модулей IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Диагностика

Используйте такие средства, как [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/)или [POST](https://www.getpostman.com/), которые позволяют задать `Accept-Encoding` заголовок запроса и изучить заголовки, размер и текст ответа. По умолчанию по промежуточного слоя для сжатия ответов сжимает ответы, соответствующие следующим условиям.

* `Accept-Encoding`Заголовок представлен со значением `gzip` , `*` или пользовательской кодировкой, соответствующей настраиваемому поставщику сжатия, который вы установили. Значение не должно быть `identity` или иметь значение свойства (квалуе,), равное `q` 0 (нулю).
* Тип MIME ( `Content-Type` ) должен быть установлен и должен соответствовать типу MIME, настроенному в <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions> .
* Запрос не должен включать `Content-Range` заголовок.
* В запросе должен использоваться небезопасный протокол (http), если в параметрах по промежуточного слоя сжатия отклика не настроен защищенный протокол (HTTPS). *Обратите внимание на опасность, [описанную выше](#compression-with-secure-protocol) , при включении безопасного сжатия содержимого.*

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Сеть для разработчиков Mozilla: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [Раздел RFC 7231 3.1.2.1. Кодирование содержимого](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [Раздел RFC 7230 4.2.3: кодирование gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Спецификация формата файла GZIP, версия 4,3](https://www.ietf.org/rfc/rfc1952.txt)

::: moniker-end
