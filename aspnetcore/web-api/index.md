---
title: Создание веб-API с помощью ASP.NET Core
author: scottaddie
description: Узнайте, как создать веб-API в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
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
uid: web-api/index
ms.openlocfilehash: fb7dfa53be1fee9904b4539a5c9da0700c2aa884
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585738"
---
# <a name="create-web-apis-with-aspnet-core"></a>Создание веб-API с помощью ASP.NET Core

Авторы: [Скотт Адди](https://github.com/scottaddie) (Scott Addie) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)

ASP.NET Core поддерживает создание служб RESTful, также известных как веб-API, с помощью C#. Для обработки запросов веб-API использует контроллеры. В веб-API *контроллеры* — это классы, производные от `ControllerBase`. В этой статье показано, как использовать контроллеры для обработки веб-запросов API.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/index/samples). ([Инструкция по скачиванию](xref:index#how-to-download-a-sample).)

## <a name="controllerbase-class"></a>Класс ControllerBase

Веб-API состоит из одного или нескольких классов контроллера, которые являются производными от <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Шаблон проекта веб-API предоставляет начальный контроллер:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Не создавайте контроллер веб-API путем наследования от класса <xref:Microsoft.AspNetCore.Mvc.Controller>. `Controller` является производным от `ControllerBase` и добавляет поддержку для представлений, обеспечивая обработку веб-страниц, а не запросов веб-API. Существует одно исключение из этого правила: если вы планируете использовать один и тот же контроллер для представлений и веб-API, сделайте его производным от `Controller`.

Класс `ControllerBase` предоставляет множество свойств и методов, которые удобны для обработки HTTP-запросов. Например, `ControllerBase.CreatedAtAction` возвращает код состояния 201.

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Вот еще примеры методов, предоставляющих `ControllerBase`.

|Метод   |Примечания    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Возвращает код состояния 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Возвращает код состояния 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Возвращает файл.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Вызывает [привязку модели](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Вызывает [проверку модели](xref:mvc/models/validation).|

Список всех доступных методов и свойств см. здесь: <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Атрибуты

Пространство имен <xref:Microsoft.AspNetCore.Mvc> предоставляет атрибуты, которые можно использовать для настройки поведения контроллеров и методов действия веб-API. В следующем примере атрибуты используются для указания поддерживаемой команды действия HTTP и всех известных кодов состояния HTTP, которые могут быть возвращены:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Вот еще примеры доступных атрибутов.

|Атрибут|Примечания|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Определяет шаблон URL-адреса для контроллера или действия.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Задает префикс и свойства, которые добавляются для привязки модели.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Определяет действие, которое поддерживает команду действия HTTP GET.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Указывает типы данных, которые принимает действие.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Указывает типы данных, которые возвращает действие.|

Список доступных атрибутов см. в пространстве имен <xref:Microsoft.AspNetCore.Mvc>.

## <a name="apicontroller-attribute"></a>Атрибут ApiController

Атрибут [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) можно применить к классу контроллера для включения следующих специализированных схем поведения API:

::: moniker range=">= aspnetcore-2.2"

* [Обязательная маршрутизация атрибутов](#attribute-routing-requirement)
* [Автоматические отклики HTTP 400](#automatic-http-400-responses)
* [Вывод параметров источника привязки](#binding-source-parameter-inference)
* [Вывод многокомпонентных запросов и запросов данных форм](#multipartform-data-request-inference)
* [Сведения о проблемах для кодов состояния ошибки](#problem-details-for-error-status-codes)

Для использования функции *Сведения о проблеме для кодов состояния ошибки* требуется [совместимая версия](xref:mvc/compatibility-version) 2.2 и выше. Для реализации других функций требуется совместимая версия 2.1 и выше.

::: moniker-end

* [Обязательная маршрутизация атрибутов](#attribute-routing-requirement)
* [Автоматические отклики HTTP 400](#automatic-http-400-responses)
* [Вывод параметров источника привязки](#binding-source-parameter-inference)
* [Вывод многокомпонентных запросов и запросов данных форм](#multipartform-data-request-inference)

Для реализации этих функций необходима [версия совместимости](xref:mvc/compatibility-version), начиная с 2.1.

### <a name="attribute-on-specific-controllers"></a>Атрибут в определенных контроллерах

Атрибут `[ApiController]` может применяться к определенным контроллерам, как показано в следующем примере из шаблона проекта:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Атрибут в нескольких контроллерах

Один из подходов к использованию атрибута на более чем одном контроллере заключается в создании пользовательского базового класса контроллера, аннотированного атрибутом `[ApiController]`. В следующем примере демонстрируется пользовательский базовый класс и производный от него контроллер:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Атрибут в сборке

Если задана [версия совместимости](xref:mvc/compatibility-version) 2.2 или последующая, атрибут `[ApiController]` можно применить к сборке. Аннотирование этим способом применяет поведение веб-API ко всем контроллерам в сборке. Его нельзя отменить для отдельных контроллеров. Примените атрибут уровня сборки к объявлению пространства имен, окружающему класс `Startup`:

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Обязательная маршрутизация атрибутов

Атрибут `[ApiController]` требует обязательной маршрутизации атрибутов. Пример:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Действия недоступны через [обычные маршруты](xref:mvc/controllers/routing#conventional-routing), определяемые `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> или <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> в `Startup.Configure`.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Действия недоступны через [обычные маршруты](xref:mvc/controllers/routing#conventional-routing), определяемые <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> или <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> в `Startup.Configure`.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Автоматические отклики HTTP 400

Благодаря атрибуту `[ApiController]` ошибки проверки модели автоматически активируют отклик HTTP 400. В результате следующий код ненужен в методе действия:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

Для выполнения предыдущей проверки ASP.NET Core MVC использует фильтр действий <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter>.

### <a name="default-badrequest-response"></a>Отклик BadRequest по умолчанию

Если задана версия совместимости 2.1, для ответов HTTP 400 по умолчанию возвращается тип отклика <xref:Microsoft.AspNetCore.Mvc.SerializableError>. Следующий текст запроса является примером сериализованного типа:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

Если задана версия совместимости 2.2 или более поздние версии, для ответов HTTP 400 по умолчанию возвращается тип отклика <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. Следующий текст запроса является примером сериализованного типа:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

Тип `ValidationProblemDetails`:

* предоставляет распознаваемый компьютером формат для указания ошибок в откликах веб-API;
* соответствует [спецификации RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

Чтобы обеспечить согласованность автоматических и настраиваемых ответов, вызовите метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A>, а не <xref:System.Web.Http.ApiController.BadRequest%2A>. `ValidationProblem` возвращает объект <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>, а также автоматический ответ.

### <a name="log-automatic-400-responses"></a>Запись в журнал автоматических откликов HTTP 400

См. описание проблемы [записи в журнал автоматических ответов HTTP 400 для ошибок проверки модели (aspnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Отключение автоматической активации отклика HTTP 400

Чтобы отключить автоматическую активацию отклика HTTP 400, задайте свойству <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> значение `true`. Добавьте выделенный ниже код в `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Вывод параметров источника привязки

Атрибут источника привязки определяет расположение, в котором находится значение параметра действия. Существуют следующие атрибуты источника привязки.

|Атрибут|Источник привязки |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Текст запроса |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Данные формы в тексте запроса |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Заголовок запроса |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Параметры строки запроса для запроса |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Данные маршрута из текущего запроса |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Служба запросов, внедренная в качестве параметра действия |

> [!WARNING]
> Не используйте `[FromRoute]`, если значения могут содержать `%2f` (то есть `/`). Для `%2f` не будет применяться отмена экранирования `/`. Используйте `[FromQuery]`, если значение может содержать `%2f`.

Без атрибута `[ApiController]` или атрибутов источника привязки, таких как `[FromQuery]`, среда выполнения ASP.NET Core попытается использовать связыватель модели для составного объекта. Связыватель модели для составного объекта извлекает данные из поставщиков значений в определенном порядке.

В следующем примере атрибут `[FromQuery]` указывает, что значение параметра `discontinuedOnly` задано в строке запроса URL-адреса для запроса:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Атрибут `[ApiController]` применяет правила зависимости к источникам данных по умолчанию для параметров действий. Эти правила избавляют от необходимости вручную определять источники привязки путем применения атрибутов к параметрам действий. Правила зависимости источника привязки работают следующим образом:

* `[FromBody]` выводится для параметров сложного типа. Исключением из правила зависимости `[FromBody]` является любой сложный встроенный тип со специальным значением, такой как <xref:Microsoft.AspNetCore.Http.IFormCollection> и <xref:System.Threading.CancellationToken>. Код определения источника привязки игнорирует эти особые типы.
* `[FromForm]` выводится для параметров действия с типом <xref:Microsoft.AspNetCore.Http.IFormFile> и <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. Он не выводится ни для каких простых или определяемых пользователем типов.
* `[FromRoute]` выводится для любого имени параметра действия, соответствующего параметру в шаблоне маршрута. Если параметру действия соответствуют несколько маршрутов, любое значение маршрута рассматривается как `[FromRoute]`.
* `[FromQuery]` выводится для любых других параметров действия.

### <a name="frombody-inference-notes"></a>Заметки о выводе FromBody

`[FromBody]` не определен для простых типов, таких как `string` или `int`. Таким образом, атрибут `[FromBody]` должен использоваться для простых типов, когда требуются эти функции.

Если у действия более одного параметра для привязки из текста запроса, выдается исключение. Например, все следующие сигнатуры метода действия вызывают исключение:

* `[FromBody]` выводится для обеих параметров, так как они являются сложными типами.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* Атрибут `[FromBody]`, заданный одному параметру, выводится для другого, так как это сложный тип.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* Атрибут `[FromBody]` выводится для обоих параметров.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> В ASP.NET Core 2.1 параметры типа коллекции, такие как списки и массивы, ошибочно выводятся как `[FromQuery]`. Для этих параметров следует использовать атрибут `[FromBody]`, если они должны быть привязаны из текста запроса. Это поведение, при котором параметры типа коллекции выводятся для привязки из текста по умолчанию, исправлено в версии ASP.NET Core, начиная с 2.2.

::: moniker-end

### <a name="disable-inference-rules"></a>Отключение правил зависимости

Чтобы отключить вывод источника привязки, задайте <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> значение `true`. Добавьте следующий код в `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Вывод многокомпонентных запросов и запросов данных форм

Атрибут `[ApiController]` позволяет применить правило зависимости, когда параметр действия аннотирован атрибутом [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute). При этом выводится тип содержимого запроса `multipart/form-data`.

Чтобы отключить поведение по умолчанию, задайте свойству <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> значение `true` в `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Сведения о проблемах для кодов состояния ошибки

Если задана версия совместимости, начиная с 2.2, MVC преобразовывает код ошибки (код состояния 400 и далее) в результат с <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. Тип `ProblemDetails` основан на [спецификации RFC 7807](https://tools.ietf.org/html/rfc7807) и предоставляет считываемые компьютером сведения об ошибке в HTTP-ответе.

Рассмотрим следующий код в действии контроллера:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Метод `NotFound` создает код состояния HTTP 404 с текстом `ProblemDetails`. Пример:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Отключение ответа ProblemDetails

Отключить автоматическое создание `ProblemDetails` для кодов состояния ошибок можно, задав свойству <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> значение `true`. Добавьте следующий код в `Startup.ConfigureServices`:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Определение поддерживаемых типов содержимого запросов с помощью атрибута [Consumes]

По умолчанию действие поддерживает все доступные типы содержимого запросов. Например, если в приложении включена поддержка [форматировщиков входных данных](xref:mvc/models/model-binding#input-formatters) JSON и XML, действие будет поддерживать несколько типов содержимого, включая `application/json` и `application/xml`.

Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов. Примените атрибут `[Consumes]` к действию или контроллеру, указав один или несколько типов содержимого:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

В приведенном выше коде действие `CreateProduct` указывает тип содержимого `application/xml`. Запросы, направляемые в это действие, должны определять заголовок `Content-Type` `application/xml`. Запросы, не определяющие заголовок `Content-Type` `application/xml`, возвращают ответ [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) (неподдерживаемый тип данных).

Атрибут `[Consumes]` также позволяет действию влиять на выбор с учетом типа содержимого входящего запроса, применяя ограничение типа. Рассмотрим следующий пример.

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

В приведенном выше коде для `ConsumesController` включена поддержка обработки запросов, отправляемых на URL-адрес `https://localhost:5001/api/Consumes`. Оба действия контроллера (`PostJson` и `PostForm`) обрабатывают запросы POST с одним и тем же URL-адресом. Если в атрибуте `[Consumes]` не применяется ограничение типа, возникает исключение неоднозначного соответствия.

Атрибут `[Consumes]` применяется к обоим действиям. Действие `PostJson` обрабатывает запросы, отправленные с заголовком `Content-Type` `application/json`. Действие `PostForm` обрабатывает запросы, отправленные с заголовком `Content-Type` `application/x-www-form-urlencoded`. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [Microsoft Learn. Создание веб-API с помощью ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
