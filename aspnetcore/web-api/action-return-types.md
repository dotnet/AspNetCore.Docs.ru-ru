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
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="11e0e-103">Типы возвращаемых значений действий контроллера в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11e0e-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="11e0e-104">Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="11e0e-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="11e0e-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11e0e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="11e0e-106">ASP.NET Core предоставляет следующие параметры для типов возвращаемых значений действий контроллера веб-API:</span><span class="sxs-lookup"><span data-stu-id="11e0e-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="11e0e-107">Определенный тип</span><span class="sxs-lookup"><span data-stu-id="11e0e-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="11e0e-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="11e0e-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="11e0e-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="11e0e-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="11e0e-110">Определенный тип</span><span class="sxs-lookup"><span data-stu-id="11e0e-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="11e0e-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="11e0e-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="11e0e-112">В этом документе объясняется, когда лучше использовать каждый тип возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="11e0e-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="11e0e-113">Определенный тип</span><span class="sxs-lookup"><span data-stu-id="11e0e-113">Specific type</span></span>

<span data-ttu-id="11e0e-114">Простейшее действие возвращает элементарный или сложный тип данных (например, `string` или пользовательский тип объекта).</span><span class="sxs-lookup"><span data-stu-id="11e0e-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="11e0e-115">Рассмотрим следующее действие, которое возвращает коллекцию пользовательских объектов `Product`:</span><span class="sxs-lookup"><span data-stu-id="11e0e-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="11e0e-116">Если не известны условия, которые необходимо соблюдать при выполнении действия, конкретного типа будет достаточно.</span><span class="sxs-lookup"><span data-stu-id="11e0e-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="11e0e-117">Предыдущее действие не принимает параметры, поэтому проверка ограничений параметров не требуется.</span><span class="sxs-lookup"><span data-stu-id="11e0e-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="11e0e-118">Если возможны множественные возвращаемые типы, то распространенным типом возвращаемого <xref:Microsoft.AspNetCore.Mvc.ActionResult> значения является примитивный или сложный тип.</span><span class="sxs-lookup"><span data-stu-id="11e0e-118">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="11e0e-119">Для выполнения этого типа действий необходимы [IActionResult](#iactionresult-type) или [ActionResult \<T> ](#actionresultt-type) .</span><span class="sxs-lookup"><span data-stu-id="11e0e-119">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="11e0e-120">В этом документе представлено несколько примеров с несколькими типами возвращаемых значений.</span><span class="sxs-lookup"><span data-stu-id="11e0e-120">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="11e0e-121">Возврат IEnumerable \<T> или иасинценумерабле\<T></span><span class="sxs-lookup"><span data-stu-id="11e0e-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="11e0e-122">В ASP.NET Core 2.2 и более ранних версиях получение интерфейса <xref:System.Collections.Generic.IEnumerable%601> из действия приводит к тому, что сериализатор выполняет синхронную итерацию операции сбора.</span><span class="sxs-lookup"><span data-stu-id="11e0e-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="11e0e-123">В результате вызовы блокируются, что может стать причиной перегрузки пула потоков.</span><span class="sxs-lookup"><span data-stu-id="11e0e-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="11e0e-124">Представьте, что Entity Framework (EF) Core используется веб-API для доступа к данным.</span><span class="sxs-lookup"><span data-stu-id="11e0e-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="11e0e-125">Во время сериализации выполняется синхронное перечисление для типа возвращаемого значения следующего действия:</span><span class="sxs-lookup"><span data-stu-id="11e0e-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="11e0e-126">Чтобы не допустить синхронного перечисления и блокировки операций ожидания для базы данных в ASP.NET Core 2.2 и более ранних версий, вызовите `ToListAsync`:</span><span class="sxs-lookup"><span data-stu-id="11e0e-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="11e0e-127">В ASP.NET Core 3.0 и более поздних версиях получение `IAsyncEnumerable<T>` из действия:</span><span class="sxs-lookup"><span data-stu-id="11e0e-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="11e0e-128">больше не приводит к синхронной итерации;</span><span class="sxs-lookup"><span data-stu-id="11e0e-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="11e0e-129">по эффективности не отличается от получения <xref:System.Collections.Generic.IEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="11e0e-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="11e0e-130">ASP.NET Core 3.0 и более поздних версий помещает результаты следующего действия в буфер перед предоставлением его сериализатору:</span><span class="sxs-lookup"><span data-stu-id="11e0e-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="11e0e-131">Мы рекомендуем объявлять тип возвращаемого значения для сигнатуры действия как `IAsyncEnumerable<T>` для гарантированного выполнения асинхронной итерации.</span><span class="sxs-lookup"><span data-stu-id="11e0e-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="11e0e-132">То есть режим итерации зависит от возвращаемого базового конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="11e0e-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="11e0e-133">MVC автоматически буферизует все конкретные типы, которые реализуют `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="11e0e-134">Рассмотрим следующее действие, которое возвращает записи о продуктах со сниженной ценой как `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="11e0e-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="11e0e-135">Эквивалентом `IAsyncEnumerable<Product>` для предшествующего действия является:</span><span class="sxs-lookup"><span data-stu-id="11e0e-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="11e0e-136">Начиная с версии ASP.NET Core 3.0, оба предшествующих действия не являются блокирующими.</span><span class="sxs-lookup"><span data-stu-id="11e0e-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="11e0e-137">Тип IActionResult</span><span class="sxs-lookup"><span data-stu-id="11e0e-137">IActionResult type</span></span>

<span data-ttu-id="11e0e-138">Тип возвращаемого значения <xref:Microsoft.AspNetCore.Mvc.IActionResult> можно использовать, если в действии допускаются несколько типов возвращаемого значения `ActionResult`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="11e0e-139">Типы `ActionResult` представляют различные коды состояния HTTP.</span><span class="sxs-lookup"><span data-stu-id="11e0e-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="11e0e-140">Любой неабстрактный класс, унаследованный от квалификаторов `ActionResult` в виде допустимого типа возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="11e0e-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="11e0e-141">Некоторые распространенные типы возвращаемых значений в этой категории: <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) и <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="11e0e-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="11e0e-142">Кроме того, удобные методы в классе <xref:Microsoft.AspNetCore.Mvc.ControllerBase> можно использовать для получения типов `ActionResult` из действия.</span><span class="sxs-lookup"><span data-stu-id="11e0e-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="11e0e-143">Например, `return BadRequest();` — это сокращенная форма `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="11e0e-144">Так как в этом типе действий существует несколько возвращаемых типов и путей, необходимо свободно использовать [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="11e0e-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="11e0e-145">Этот атрибут создает описательные сведения об ответе для страниц справки по веб-API, создаваемых с помощью таких инструментов, как [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="11e0e-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="11e0e-146">`[ProducesResponseType]` указывает известные типы и коды состояния HTTP, возвращаемые действием.</span><span class="sxs-lookup"><span data-stu-id="11e0e-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="11e0e-147">Синхронное действие</span><span class="sxs-lookup"><span data-stu-id="11e0e-147">Synchronous action</span></span>

<span data-ttu-id="11e0e-148">Рассмотрим следующее синхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="11e0e-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="11e0e-149">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="11e0e-149">In the preceding action:</span></span>

* <span data-ttu-id="11e0e-150">возвращается код состояния 404, если продукт, представленный `id`, не существует в базовом хранилище данных;</span><span class="sxs-lookup"><span data-stu-id="11e0e-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="11e0e-151">Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> вызывается как сокращение для `return new NotFoundResult();`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="11e0e-152">Код состояния 200 возвращается с объектом `Product`, если продукт не существует.</span><span class="sxs-lookup"><span data-stu-id="11e0e-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="11e0e-153">Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> вызывается как сокращение для `return new OkObjectResult(product);`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="11e0e-154">Асинхронное действие</span><span class="sxs-lookup"><span data-stu-id="11e0e-154">Asynchronous action</span></span>

<span data-ttu-id="11e0e-155">Рассмотрим следующее асинхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="11e0e-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="11e0e-156">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="11e0e-156">In the preceding action:</span></span>

* <span data-ttu-id="11e0e-157">Код состояния 400 возвращается, если описание продукта содержит строку XYZ Widget.</span><span class="sxs-lookup"><span data-stu-id="11e0e-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="11e0e-158">Удобный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> вызывается как сокращение для `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="11e0e-159">Код состояния 201 генерируется удобным методом <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> при создании продукта.</span><span class="sxs-lookup"><span data-stu-id="11e0e-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="11e0e-160">В качестве альтернативы вызову `CreatedAtAction` можно использовать `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="11e0e-161">В этом пути к коду объект `Product` предоставляется в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="11e0e-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="11e0e-162">Также предоставляется заголовок ответа `Location` с URL-адресом только что созданного продукта.</span><span class="sxs-lookup"><span data-stu-id="11e0e-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="11e0e-163">Например, следующая модель указывает на то, что запросы должны включать свойства `Name` и `Description`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="11e0e-164">Если `Name` и `Description` не были указаны в запросе, происходит сбой проверки модели.</span><span class="sxs-lookup"><span data-stu-id="11e0e-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="11e0e-165">Если [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) применяется атрибут в ASP.NET Core 2,1 или более поздней версии, ошибки проверки модели приводят к коду состояния 400.</span><span class="sxs-lookup"><span data-stu-id="11e0e-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="11e0e-166">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="11e0e-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="11e0e-167">\<T>Тип ActionResult</span><span class="sxs-lookup"><span data-stu-id="11e0e-167">ActionResult\<T> type</span></span>

<span data-ttu-id="11e0e-168">В ASP.NET Core 2,1 введен тип возвращаемого значения [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) для действий контроллера веб-API.</span><span class="sxs-lookup"><span data-stu-id="11e0e-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="11e0e-169">Он позволяет возвращать тип, производный от <xref:Microsoft.AspNetCore.Mvc.ActionResult> или [определенный тип](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="11e0e-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="11e0e-170">`ActionResult<T>` имеет следующие преимущества по сравнению с [типом IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="11e0e-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="11e0e-171">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) `Type` Свойство атрибута можно исключить.</span><span class="sxs-lookup"><span data-stu-id="11e0e-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="11e0e-172">Например, `[ProducesResponseType(200, Type = typeof(Product))]` упрощается до `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="11e0e-173">Ожидаемый тип возвращаемого значения действия вместо этого выводится из `T` в `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="11e0e-174">[Операторы неявного приведения](/dotnet/csharp/language-reference/keywords/implicit) поддерживают преобразование `T` и `ActionResult` в `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="11e0e-175">`T` преобразуется в <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, то есть `return new ObjectResult(T);` упрощается до `return T;`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="11e0e-176">C# не поддерживает операторы неявных приведений в интерфейсах.</span><span class="sxs-lookup"><span data-stu-id="11e0e-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="11e0e-177">Следовательно, для преобразования в конкретный тип необходимо использовать `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="11e0e-178">Например, использование `IEnumerable` не работает в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="11e0e-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="11e0e-179">Один из способов исправить приведенный выше код — возвратить `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="11e0e-180">Большинство действий имеют тип возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="11e0e-180">Most actions have a specific return type.</span></span> <span data-ttu-id="11e0e-181">При выполнении действия могут возникнуть непредвиденные условия, и в этом случае определенный тип не возвращается.</span><span class="sxs-lookup"><span data-stu-id="11e0e-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="11e0e-182">Например, входной параметр действия может не пройти проверку модели.</span><span class="sxs-lookup"><span data-stu-id="11e0e-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="11e0e-183">В этом случае обычно возвращается подходящий тип `ActionResult` вместо конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="11e0e-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="11e0e-184">Синхронное действие</span><span class="sxs-lookup"><span data-stu-id="11e0e-184">Synchronous action</span></span>

<span data-ttu-id="11e0e-185">Рассмотрим синхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="11e0e-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="11e0e-186">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="11e0e-186">In the preceding action:</span></span>

* <span data-ttu-id="11e0e-187">возвращается код состояния 404, если продукт не существует в базе данных;</span><span class="sxs-lookup"><span data-stu-id="11e0e-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="11e0e-188">возвращается код состояния 200 с соответствующим объектом `Product`, если продукт существует.</span><span class="sxs-lookup"><span data-stu-id="11e0e-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="11e0e-189">До версии ASP.NET Core 2.1 строка `return product;` имела бы вид `return Ok(product);`.</span><span class="sxs-lookup"><span data-stu-id="11e0e-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="11e0e-190">Асинхронное действие</span><span class="sxs-lookup"><span data-stu-id="11e0e-190">Asynchronous action</span></span>

<span data-ttu-id="11e0e-191">Рассмотрим асинхронное действие, в котором возможны два типа возвращаемых значений:</span><span class="sxs-lookup"><span data-stu-id="11e0e-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="11e0e-192">В предшествующем действии:</span><span class="sxs-lookup"><span data-stu-id="11e0e-192">In the preceding action:</span></span>

* <span data-ttu-id="11e0e-193">Код состояния 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) возвращается средой выполнения ASP.NET Core, если:</span><span class="sxs-lookup"><span data-stu-id="11e0e-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="11e0e-194">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут применен, и проверка модели завершается неудачно.</span><span class="sxs-lookup"><span data-stu-id="11e0e-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="11e0e-195">Описание продукта содержит XYZ Widget.</span><span class="sxs-lookup"><span data-stu-id="11e0e-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="11e0e-196">Код состояния 201 генерируется методом <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> при создании продукта.</span><span class="sxs-lookup"><span data-stu-id="11e0e-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="11e0e-197">В этом пути к коду объект `Product` предоставляется в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="11e0e-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="11e0e-198">Также предоставляется заголовок ответа `Location` с URL-адресом только что созданного продукта.</span><span class="sxs-lookup"><span data-stu-id="11e0e-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="11e0e-199">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="11e0e-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
