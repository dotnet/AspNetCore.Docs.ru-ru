---
title: Форматирование данных отклика в веб-API ASP.NET Core
author: rick-anderson
description: Сведения о форматировании данных отклика в веб-API ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5d228af00ee34e7f8ca60a5085872fdb93842367
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057503"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Форматирование данных отклика в веб-API ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)

Приложение MVC ASP.NET Core поддерживает форматирование данных ответа. Данные ответа могут возвращаться в определенных форматах или в формате, запрошенном клиентом.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Результаты действий для конкретного формата

Некоторые типы результатов действий характерны для определенного формата, например <xref:Microsoft.AspNetCore.Mvc.JsonResult> и <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Действия могут возвращать результаты в определенном формате независимо от настроек клиента. Например, при возврате `JsonResult` возвращаются данные в формате JSON. При возврате `ContentResult` или строки возвращаются строковые данные в формате обычного текста.

Действие не должно возвращать данные конкретного типа. ASP.NET Core поддерживает любое возвращаемое значение объекта.  Результаты из действий, возвращающих объекты, которые не являются типами <xref:Microsoft.AspNetCore.Mvc.IActionResult>, сериализуются с помощью соответствующей реализации <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>. Для получения дополнительной информации см. <xref:web-api/action-return-types>.

Встроенный вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> возвращает данные в формате JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Скачанный пример возвращает список авторов. При использовании средств разработчика в браузере (F12) или [Postman](https://www.getpostman.com/tools) с предыдущим кодом:

* Отобразится заголовок ответа, содержащий **content-type:** `application/json; charset=utf-8`.
* Отобразятся заголовки запросов. Например, заголовок `Accept`. Приведенный выше код игнорирует заголовок `Accept`.

Чтобы возвратить данные в формате обычного текста, используйте <xref:Microsoft.AspNetCore.Mvc.ContentResult> и вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

В приведенном выше коде возвращаемым `Content-Type` является `text/plain`. При возврате строки возвращается `Content-Type` со значением `text/plain`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Для действий с несколькими возвращаемыми типами возвращается значение `IActionResult`. Например, возвращаются различные коды состояния HTTP на основе результатов выполненных операций.

## <a name="content-negotiation"></a>Согласование содержимого

Согласование содержимого происходит, когда клиент задает [заголовок Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Для ASP.NET Core по умолчанию используется формат [JSON](https://json.org/). Согласование содержимого:

* реализуется с помощью <xref:Microsoft.AspNetCore.Mvc.ObjectResult>;
* встроено в результаты действия с определенным кодом состояния, возвращаемые из вспомогательных методов; вспомогательные методы результатов действия основаны на `ObjectResult`;

при возврате типа модели возвращаемым типом является `ObjectResult`.

Следующий метод действия использует вспомогательные методы `Ok` и `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

По умолчанию ASP.NET Core поддерживает типы мультимедиа `application/json`, `text/json` и `text/plain`. Средства, такие как [Fiddler](https://www.telerik.com/fiddler) или [Postman](https://www.getpostman.com/tools), могут установить заголовок запроса `Accept`, чтобы указать формат возвращаемого значения. Если заголовок `Accept` содержит тип, который поддерживается сервером, возвращается этот тип. Сведения о добавлении дополнительных форматировщиков приведены в следующем разделе.

Действия контроллера могут возвращать POCO (простые объекты CLR). При возвращении POCO среда выполнения автоматически создает `ObjectResult`, который генерирует оболочку объекта. Клиент получает отформатированный сериализованный объект. Если возвращаемый объект имеет значение `null`, возвращается ответ `204 No Content`.

Возвращение типа объекта:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

В приведенном выше коде запрос допустимого псевдонима автора получает ответ `200 OK` с данными об авторе. Запрос недопустимого псевдонима возвращает ответ `204 No Content`.

### <a name="the-accept-header"></a>Заголовок Accept

*Согласование* содержимого выполняется только при наличии в запросе заголовка `Accept`. Если запрос содержит заголовок Accept, ASP.NET Core:

* перечисляет типы мультимедиа в заголовке Accept в порядке предпочтения;
* пытается найти форматировщик, который может создать ответ в одном из указанных форматов.

Если форматировщик, который может удовлетворить запрос клиента, не найден, ASP.NET Core:

* Возвращает `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> , если имеет значение `true` , или-
* пытается найти первый форматировщик, который может создать ответ.

Если форматировщик, обеспечивающий требуемый формат, не настроен, используется первый форматировщик, способный отформатировать данный объект. Если в запросе не отображается заголовок `Accept`:

* Для сериализации ответа используется первый форматировщик, который может работать с объектом.
* Согласование не выполняется. Сервер определяет возвращаемый формат.

Если заголовок Accept содержит `*/*`, он игнорируется, если только `RespectBrowserAcceptHeader` не имеет значение true в <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.

### <a name="browsers-and-content-negotiation"></a>Браузеры и согласование содержимого

В отличие от обычных клиентов API, веб-браузеры предоставляют заголовки `Accept`. Веб-браузер определяет множество форматов, включая подстановочные знаки. Если платформа обнаруживает, что запрос поступает из браузера, по умолчанию выполняется следующее:

* Заголовок `Accept` игнорируется.
* Содержимое возвращается в формате JSON, если не указано иначе.

Это обеспечивает более согласованное взаимодействие между браузерами при использовании API.

Чтобы настроить приложение для учета заголовков Accept в браузере, установите для параметра <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> значение `true`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Настройка форматировщиков

В приложениях, которым требуется поддержка дополнительных форматов, можно добавлять соответствующие пакеты NuGet и настраивать поддержку. Существуют отдельные модули форматирования для ввода и вывода. Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding). Форматировщики вывода используются для форматирования ответов. Сведения о создании пользовательского форматировщика см. в статье [Пользовательские модули форматирования для веб-API в ASP.NET Core](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Добавление поддержки формата XML

Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Приведенный выше код сериализует результаты с помощью `XmlSerializer`.

При использовании приведенного выше кода методы контроллера возвращают соответствующий формат на основе заголовка `Accept` запроса.

### <a name="configure-systemtextjson-based-formatters"></a>Настройка модулей форматирования System.Text.Jsна основе

Функции для `System.Text.Json` модулей форматирования на основе можно настроить с помощью <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> . Форматирование по умолчанию — camelCase. Следующий выделенный код задает форматирование PascalCase:

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

Следующий метод действия вызывает [ControllerBase. проблема](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) , чтобы создать <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> Ответ:

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

С приведенным выше кодом:

  * `https://localhost:5001/WeatherForecast/temperature` Возвращает PascalCase.
  * `https://localhost:5001/WeatherForecast/error` Возвращает camelCase. Ответ об ошибке всегда camelCase, даже если приложение устанавливает формат PascalCase. `ProblemDetails` соответствует стандарту [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), который указывает нижний регистр

Следующий код задает PascalCase и добавляет пользовательский преобразователь:

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`. Например:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Добавление поддержки формата JSON на основе Newtonsoft.Json

До выпуска версии ASP.NET Core 3.0 по умолчанию использовались форматировщики JSON, реализованные с помощью пакета `Newtonsoft.Json`. В ASP.NET Core 3.0 или более поздней версии форматировщики JSON по умолчанию основаны на `System.Text.Json`. Поддержка `Newtonsoft.Json` модулей форматирования и функций на основе данных доступна путем установки [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) пакета NuGet и его настройки в `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

В приведенном выше коде вызов `AddNewtonsoftJson` настраивает следующие функции веб-API, MVC и Razor страниц для использования `Newtonsoft.Json` :

* Модули форматирования ввода и вывода, считывающие и записывающие JSON
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [JSON PATCH](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [TempData](xref:fundamentals/app-state#tempdata)

Возможно, некоторые функции не будут оптимально работать с форматировщиками на основе `System.Text.Json` и будут требовать ссылки на форматировщики на основе `Newtonsoft.Json`. Продолжайте использовать форматировщики на основе `Newtonsoft.Json`, если приложение:

* Использует атрибут `Newtonsoft.Json`. Например, `[JsonProperty]` или `[JsonIgnore]`.
* Настраивает параметры сериализации.
* Зависит от функций, предоставляемых `Newtonsoft.Json`.
* Настраивается `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. В ASP.NET Core более ранних версий, чем версия 3.0, `JsonResult.SerializerSettings` принимает экземпляр `JsonSerializerSettings`, который относится к `Newtonsoft.Json`.
* Создается документация [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Функции для форматировщиков на основе `Newtonsoft.Json` можно настроить с помощью `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`. Например:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Добавление поддержки формата XML

Для форматирования XML требуется пакет NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).

Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Приведенный выше код сериализует результаты с помощью `XmlSerializer`.

При использовании приведенного выше кода методы контроллера должны возвращать соответствующий формат на основе заголовка `Accept` запроса.

::: moniker-end

### <a name="specify-a-format"></a>Указание формата

Чтобы ограничить форматы ответа, примените [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) фильтр. Как и большинство [фильтров](xref:mvc/controllers/filters), `[Produces]` можно применить к действию, контроллеру или глобальной области:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Предыдущий [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Фильтр:

* Заставляет все действия в контроллере возвращать ответы в формате JSON.
* Если другие форматировщики настроены и клиент указывает другой формат, возвращается JSON.

Дополнительные сведения см. в статье [Фильтры в ASP.NET Core](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Специальные форматировщики

Встроенные модули форматирования реализуют некоторые специальные возможности. По умолчанию типы возвращаемых значений `string` форматируются как *text/plain* (*text/html*, если того требует заголовок `Accept`). Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>. Форматировщики удаляются в методе `ConfigureServices`. Действия, у которых типом возвращаемого объекта является модель, возвращают ответ `204 No Content` при возврате значения `null`. Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>. Приведенный ниже код удаляет `StringOutputFormatter` и `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Без `StringOutputFormatter`, встроенный модуль форматирования JSON форматирует типы возвращаемого значения `string`. Если встроенный модуль форматирования JSON удален и доступен модуль форматирования XML, то типы возвращаемого значения `string` форматирует модуль форматирования XML. В противном случае, `string` типы возвращаемого значения возвращают `406 Not Acceptable`.

Без `HttpNoContentOutputFormatter` объекты со значением null форматируются с помощью настроенного модуля форматирования. Например:

* Форматировщик JSON возвращает ответ с текстом `null`.
* Форматировщик XML возвращает пустой XML-элемент с атрибутом `xsi:nil="true"`.

## <a name="response-format-url-mappings"></a>Сопоставления URL-адреса для формата ответа

Клиенты могут запрашивать определенный формат как часть URL-адреса, например:

* В строке запроса или в части пути.
* С использованием расширения файла конкретного формата, такого как XML или JSON.

Сопоставление из пути запроса должно быть указано в маршруте, используемом API. Например:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Этот маршрут позволяет задать запрошенный формат в качестве дополнительного расширения файла. [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Атрибут проверяет наличие значения формата в `RouteData` и сопоставляет формат ответа с соответствующим модулем форматирования при создании ответа.

|           Маршрут        |             Formatter              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Модуль форматирования вывода по умолчанию    |
| `/api/products/5.json` | Модуль форматирования JSON (если настроен) |
| `/api/products/5.xml`  | Модуль форматирования XML (если настроен)  |
