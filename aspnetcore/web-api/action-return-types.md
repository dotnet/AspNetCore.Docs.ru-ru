---
title: Типы возвращаемых значений действий контроллера в веб-API ASP.NET Core
author: scottaddie
description: Узнайте, как использовать разные типы возвращаемых значений методов действий контроллера в веб-API ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: 62c8227ca770a3a9adbe780685b140bc0e86841e
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710702"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Типы возвращаемых значений действий контроллера в веб-API ASP.NET Core

Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([как скачивать](xref:index#how-to-download-a-sample))

ASP.NET Core предоставляет следующие параметры для типов возвращаемых значений действий контроллера веб-API:

::: moniker range=">= aspnetcore-2.1"

* [Определенный тип](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Определенный тип](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

В этом документе объясняется, когда лучше использовать каждый тип возвращаемого значения.

## <a name="specific-type"></a>Определенный тип

Простейшее действие возвращает элементарный или сложный тип данных (например, `string` или пользовательский тип объекта). Рассмотрим следующее действие, которое возвращает коллекцию пользовательских объектов `Product`:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Если не известны условия, которые необходимо соблюдать при выполнении действия, конкретного типа будет достаточно. Предыдущее действие не принимает параметры, поэтому проверка ограничений параметров не требуется.

Если возможны множественные возвращаемые типы, то распространенным типом возвращаемого <xref:Microsoft.AspNetCore.Mvc.ActionResult> значения является примитивный или сложный тип. Для выполнения этого типа действий необходимы [IActionResult](#iactionresult-type) или [ActionResult \<T> ](#actionresultt-type) . В этом документе представлено несколько примеров с несколькими типами возвращаемых значений.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Возврат IEnumerable \<T> или иасинценумерабле\<T>

В ASP.NET Core 2.2 и более ранних версиях получение интерфейса <xref:System.Collections.Generic.IEnumerable%601> из действия приводит к тому, что сериализатор выполняет синхронную итерацию операции сбора. В результате вызовы блокируются, что может стать причиной перегрузки пула потоков. Представьте, что Entity Framework (EF) Core используется веб-API для доступа к данным. Во время сериализации выполняется синхронное перечисление для типа возвращаемого значения следующего действия:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Чтобы не допустить синхронного перечисления и блокировки операций ожидания для базы данных в ASP.NET Core 2.2 и более ранних версий, вызовите `ToListAsync`:

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

В ASP.NET Core 3.0 и более поздних версиях получение `IAsyncEnumerable<T>` из действия:

* больше не приводит к синхронной итерации;
* по эффективности не отличается от получения <xref:System.Collections.Generic.IEnumerable%601>.

ASP.NET Core 3.0 и более поздних версий помещает результаты следующего действия в буфер перед предоставлением его сериализатору:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Мы рекомендуем объявлять тип возвращаемого значения для сигнатуры действия как `IAsyncEnumerable<T>` для гарантированного выполнения асинхронной итерации. То есть режим итерации зависит от возвращаемого базового конкретного типа. MVC автоматически буферизует все конкретные типы, которые реализуют `IAsyncEnumerable<T>`.

Рассмотрим следующее действие, которое возвращает записи о продуктах со сниженной ценой как `IEnumerable<Product>`:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

Эквивалентом `IAsyncEnumerable<Product>` для предшествующего действия является:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Начиная с версии ASP.NET Core 3.0, оба предшествующих действия не являются блокирующими.

## <a name="iactionresult-type"></a>Тип IActionResult

Тип возвращаемого значения <xref:Microsoft.AspNetCore.Mvc.IActionResult> можно использовать, если в действии допускаются несколько типов возвращаемого значения `ActionResult`. Типы `ActionResult` представляют различные коды состояния HTTP. Любой неабстрактный класс, унаследованный от квалификаторов `ActionResult` в виде допустимого типа возвращаемого значения. Некоторые распространенные типы возвращаемых значений в этой категории: <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) и <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Кроме того, удобные методы в классе <xref:Microsoft.AspNetCore.Mvc.ControllerBase> можно использовать для получения типов `ActionResult` из действия. Например, `return BadRequest();` — это сокращенная форма `return new BadRequestResult();`.

Так как в этом типе действий существует несколько возвращаемых типов и путей, необходимо свободно использовать [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) атрибут. Этот атрибут создает описательные сведения об ответе для страниц справки по веб-API, создаваемых с помощью таких инструментов, как [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` указывает известные типы и коды состояния HTTP, возвращаемые действием.

### <a name="synchronous-action"></a>Синхронное действие

Рассмотрим следующее синхронное действие, в котором возможны два типа возвращаемых значений:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

В предшествующем действии:

* возвращается код состояния 404, если продукт, представленный `id`, не существует в базовом хранилище данных; Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> вызывается как сокращение для `return new NotFoundResult();`.
* Код состояния 200 возвращается с объектом `Product`, если продукт не существует. Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> вызывается как сокращение для `return new OkObjectResult(product);`.

### <a name="asynchronous-action"></a>Асинхронное действие

Рассмотрим следующее асинхронное действие, в котором возможны два типа возвращаемых значений:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

В предшествующем действии:

* Код состояния 400 возвращается, если описание продукта содержит строку XYZ Widget. Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> вызывается как сокращение для `return new BadRequestResult();`.
* Код состояния 201 генерируется удобным методом <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> при создании продукта. В качестве альтернативы вызову `CreatedAtAction` можно использовать `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. В этом пути к коду объект `Product` предоставляется в тексте ответа. Также предоставляется заголовок ответа `Location` с URL-адресом только что созданного продукта.

Например, следующая модель указывает на то, что запросы должны включать свойства `Name` и `Description`. Если `Name` и `Description` не были указаны в запросе, происходит сбой проверки модели.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Если [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) применяется атрибут в ASP.NET Core 2,1 или более поздней версии, ошибки проверки модели приводят к коду состояния 400. Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>\<T>Тип ActionResult

В ASP.NET Core 2,1 введен тип возвращаемого значения [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) для действий контроллера веб-API. Он позволяет возвращать тип, производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult> или [определенный тип](#specific-type). `ActionResult<T>` имеет следующие преимущества по сравнению с [типом IActionResult](#iactionresult-type):

* [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Свойство атрибута можно исключить. Например, `[ProducesResponseType(200, Type = typeof(Product))]` упрощается до `[ProducesResponseType(200)]`. Ожидаемый тип возвращаемого значения действия вместо этого выводится из `T` в `ActionResult<T>`.
* [Операторы неявного приведения](/dotnet/csharp/language-reference/keywords/implicit) поддерживают преобразование `T` и `ActionResult` в `ActionResult<T>`. `T` преобразуется в <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, то есть `return new ObjectResult(T);` упрощается до `return T;`.

C# не поддерживает операторы неявных приведений в интерфейсах. Следовательно, для преобразования в конкретный тип необходимо использовать `ActionResult<T>`. Например, использование `IEnumerable` не работает в следующем примере:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Один из способов исправить приведенный выше код — возвратить `_repository.GetProducts().ToList();`.

Большинство действий имеют тип возвращаемого значения. При выполнении действия могут возникнуть непредвиденные условия, и в этом случае определенный тип не возвращается. Например, входной параметр действия может не пройти проверку модели. В этом случае обычно возвращается подходящий тип `ActionResult` вместо конкретного типа.

### <a name="synchronous-action"></a>Синхронное действие

Рассмотрим синхронное действие, в котором возможны два типа возвращаемых значений:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

В предшествующем действии:

* возвращается код состояния 404, если продукт не существует в базе данных;
* возвращается код состояния 200 с соответствующим объектом `Product`, если продукт существует. До версии ASP.NET Core 2.1 строка `return product;` имела бы вид `return Ok(product);`.

### <a name="asynchronous-action"></a>Асинхронное действие

Рассмотрим асинхронное действие, в котором возможны два типа возвращаемых значений:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

В предшествующем действии:

* Код состояния 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) возвращается средой выполнения ASP.NET Core, если:
  * [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут применен, и проверка модели завершается неудачно.
  * Описание продукта содержит XYZ Widget.
* Код состояния 201 генерируется методом <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> при создании продукта. В этом пути к коду объект `Product` предоставляется в тексте ответа. Также предоставляется заголовок ответа `Location` с URL-адресом только что созданного продукта.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
