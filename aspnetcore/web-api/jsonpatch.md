---
title: JsonPatch в веб-API ASP.NET Core
author: rick-anderson
description: Сведения об обработке запросов JSON Patch в веб-API ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 4ed44a0fca9e0834a78e433cdd48cbd153c58666
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587857"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="e30f9-103">JsonPatch в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e30f9-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="e30f9-104">Авторы: [Том Дикстра](https://github.com/tdykstra) (Tom Dykstra) и [Kirk Larkin](https://github.com/serpent5) (Кирк Ларкин)</span><span class="sxs-lookup"><span data-stu-id="e30f9-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e30f9-105">В этой статье описывается обработка запросов JSON Patch в веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e30f9-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="e30f9-106">Установка пакета</span><span class="sxs-lookup"><span data-stu-id="e30f9-106">Package installation</span></span>

<span data-ttu-id="e30f9-107">Чтобы включить поддержку исправления JSON в приложении, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="e30f9-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="e30f9-108">Установите [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) пакет NuGet.</span><span class="sxs-lookup"><span data-stu-id="e30f9-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="e30f9-109">Обновите метод проекта `Startup.ConfigureServices` для вызова <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="e30f9-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="e30f9-110">Пример:</span><span class="sxs-lookup"><span data-stu-id="e30f9-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="e30f9-111">`AddNewtonsoftJson` совместим с методами регистрации службы MVC:</span><span class="sxs-lookup"><span data-stu-id="e30f9-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="e30f9-112">Исправление JSON, Аддневтонсофтжсон и System.Text.Jsна</span><span class="sxs-lookup"><span data-stu-id="e30f9-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="e30f9-113">`AddNewtonsoftJson` заменяет `System.Text.Json` модули форматирования ввода и вывода, используемые для форматирования **всего** содержимого JSON.</span><span class="sxs-lookup"><span data-stu-id="e30f9-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="e30f9-114">Чтобы добавить поддержку обновления JSON с помощью `Newtonsoft.Json` , при этом другие модули форматирования не меняются, обновите метод проекта следующим образом `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e30f9-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="e30f9-115">Для приведенного выше кода требуется `Microsoft.AspNetCore.Mvc.NewtonsoftJson` пакет и следующие `using` инструкции:</span><span class="sxs-lookup"><span data-stu-id="e30f9-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="e30f9-116">Метод HTTP-запроса PATCH</span><span class="sxs-lookup"><span data-stu-id="e30f9-116">PATCH HTTP request method</span></span>

<span data-ttu-id="e30f9-117">Методы PUT и [PATCH](https://tools.ietf.org/html/rfc5789) используются для обновления существующего ресурса.</span><span class="sxs-lookup"><span data-stu-id="e30f9-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="e30f9-118">Различие между ними заключается в том, что PUT заменяет весь ресурс, а PATCH указывает только изменения.</span><span class="sxs-lookup"><span data-stu-id="e30f9-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="e30f9-119">JSON PATCH</span><span class="sxs-lookup"><span data-stu-id="e30f9-119">JSON Patch</span></span>

<span data-ttu-id="e30f9-120">Формат [JSON Patch](https://tools.ietf.org/html/rfc6902) используется для указания обновлений, применяемых к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="e30f9-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="e30f9-121">Документ JSON Patch содержит массив *операций*.</span><span class="sxs-lookup"><span data-stu-id="e30f9-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="e30f9-122">Каждая операция определяет конкретный тип изменения.</span><span class="sxs-lookup"><span data-stu-id="e30f9-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="e30f9-123">Примерами таких изменений являются добавление элемента массива или замена значения свойства.</span><span class="sxs-lookup"><span data-stu-id="e30f9-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="e30f9-124">Например, следующие документы JSON представляют ресурс, документ исправления JSON для ресурса и результат применения операций исправления.</span><span class="sxs-lookup"><span data-stu-id="e30f9-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="e30f9-125">Пример ресурса</span><span class="sxs-lookup"><span data-stu-id="e30f9-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="e30f9-126">Пример JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e30f9-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="e30f9-127">В приведенном выше документе JSON:</span><span class="sxs-lookup"><span data-stu-id="e30f9-127">In the preceding JSON:</span></span>

* <span data-ttu-id="e30f9-128">свойство `op` указывает тип операции;</span><span class="sxs-lookup"><span data-stu-id="e30f9-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="e30f9-129">свойство `path` указывает обновляемый элемент;</span><span class="sxs-lookup"><span data-stu-id="e30f9-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="e30f9-130">свойство `value` предоставляет новое значение.</span><span class="sxs-lookup"><span data-stu-id="e30f9-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="e30f9-131">Ресурс после обновления</span><span class="sxs-lookup"><span data-stu-id="e30f9-131">Resource after patch</span></span>

<span data-ttu-id="e30f9-132">Ниже показан ресурс в том состоянии, которое он принимает после применения приведенного выше документа JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e30f9-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="e30f9-133">Изменения, внесенные путем применения документа исправления JSON к ресурсу, являются атомарными.</span><span class="sxs-lookup"><span data-stu-id="e30f9-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="e30f9-134">Если какая-либо операция в списке завершается ошибкой, операция в списке не применяется.</span><span class="sxs-lookup"><span data-stu-id="e30f9-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="e30f9-135">Синтаксис Path</span><span class="sxs-lookup"><span data-stu-id="e30f9-135">Path syntax</span></span>

<span data-ttu-id="e30f9-136">Свойство [Path](https://tools.ietf.org/html/rfc6901) в объекте операции содержит косые черты между уровнями.</span><span class="sxs-lookup"><span data-stu-id="e30f9-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="e30f9-137">Например, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="e30f9-138">Для указания элементов массива используются числовые индексы, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="e30f9-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="e30f9-139">Первый элемент массива `addresses` будет обозначаться как `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="e30f9-140">В `add` конец массива используйте дефис ( `-` ), а не номер индекса: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="e30f9-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="e30f9-141">Operations</span><span class="sxs-lookup"><span data-stu-id="e30f9-141">Operations</span></span>

<span data-ttu-id="e30f9-142">В следующей таблице перечислены поддерживаемые операции, которые определены в [спецификации JSON Patch](https://tools.ietf.org/html/rfc6902).</span><span class="sxs-lookup"><span data-stu-id="e30f9-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="e30f9-143">Операция</span><span class="sxs-lookup"><span data-stu-id="e30f9-143">Operation</span></span>  | <span data-ttu-id="e30f9-144">Примечания</span><span class="sxs-lookup"><span data-stu-id="e30f9-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="e30f9-145">Добавляет свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="e30f9-145">Add a property or array element.</span></span> <span data-ttu-id="e30f9-146">Для существующего свойства устанавливает значение.</span><span class="sxs-lookup"><span data-stu-id="e30f9-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="e30f9-147">Удаляет свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="e30f9-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="e30f9-148">Действует так же, как `remove` с последующим `add` в том же расположении.</span><span class="sxs-lookup"><span data-stu-id="e30f9-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="e30f9-149">Действует так же, как `remove` из источника с последующим `add`, в котором указаны место назначения и значение из источника.</span><span class="sxs-lookup"><span data-stu-id="e30f9-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="e30f9-150">Действует так же, как `add`, в котором указаны место назначения и значение из источника.</span><span class="sxs-lookup"><span data-stu-id="e30f9-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="e30f9-151">Возвращает успешный код состояния, если значение `path` совпадает с предоставленным `value`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="e30f9-152">Обновление JSON в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e30f9-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="e30f9-153">Реализация ASP.NET Core для JSON Patch предоставляется в пакете NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="e30f9-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="e30f9-154">Код метода действия</span><span class="sxs-lookup"><span data-stu-id="e30f9-154">Action method code</span></span>

<span data-ttu-id="e30f9-155">В контроллере API есть метод действия для JSON Patch, который:</span><span class="sxs-lookup"><span data-stu-id="e30f9-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="e30f9-156">помечен атрибутом `HttpPatch`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="e30f9-157">принимает `JsonPatchDocument<T>` обычно с указанием `[FromBody]`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="e30f9-158">вызывает `ApplyTo` для целевого документа, чтобы применить изменения.</span><span class="sxs-lookup"><span data-stu-id="e30f9-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="e30f9-159">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="e30f9-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="e30f9-160">Этот код из примера приложения работает со следующей `Customer` моделью:</span><span class="sxs-lookup"><span data-stu-id="e30f9-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="e30f9-161">Этот пример метода действия:</span><span class="sxs-lookup"><span data-stu-id="e30f9-161">The sample action method:</span></span>

* <span data-ttu-id="e30f9-162">Создает документ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="e30f9-163">применяет изменения;</span><span class="sxs-lookup"><span data-stu-id="e30f9-163">Applies the patch.</span></span>
* <span data-ttu-id="e30f9-164">возвращает результат в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="e30f9-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="e30f9-165">В реальном приложении код будет извлекать данные из хранилища, например из базы данных, и обновлять эту базу данных после применения исправлений.</span><span class="sxs-lookup"><span data-stu-id="e30f9-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="e30f9-166">Состояние модели</span><span class="sxs-lookup"><span data-stu-id="e30f9-166">Model state</span></span>

<span data-ttu-id="e30f9-167">Указанный выше пример метода действия вызывает перегрузку `ApplyTo`, которая принимает состояние модели в качестве одного из параметров.</span><span class="sxs-lookup"><span data-stu-id="e30f9-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="e30f9-168">В этом случае вы получаете в ответах сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="e30f9-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="e30f9-169">В следующем примере показан текст ответа с кодом 400 "Неверный запрос" для операции `test`:</span><span class="sxs-lookup"><span data-stu-id="e30f9-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="e30f9-170">Динамические объекты</span><span class="sxs-lookup"><span data-stu-id="e30f9-170">Dynamic objects</span></span>

<span data-ttu-id="e30f9-171">В следующем примере метода действия показано, как применить исправление к динамическому объекту.</span><span class="sxs-lookup"><span data-stu-id="e30f9-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="e30f9-172">Операция добавления</span><span class="sxs-lookup"><span data-stu-id="e30f9-172">The add operation</span></span>

* <span data-ttu-id="e30f9-173">Если `path` указывает на элемент массива, новый элемент вставляется перед тем, который указан в параметре `path`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="e30f9-174">Если `path` указывает на свойство, задается значение свойства.</span><span class="sxs-lookup"><span data-stu-id="e30f9-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="e30f9-175">Если `path` указывает на несуществующее расположение:</span><span class="sxs-lookup"><span data-stu-id="e30f9-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="e30f9-176">Если обновляемый ресурс является динамическим объектом, добавляется свойство.</span><span class="sxs-lookup"><span data-stu-id="e30f9-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="e30f9-177">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e30f9-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="e30f9-178">Следующий пример документа с исправлениями устанавливает значение `CustomerName` и добавляет объект `Order` в конец массива `Orders`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="e30f9-179">Операция удаления</span><span class="sxs-lookup"><span data-stu-id="e30f9-179">The remove operation</span></span>

* <span data-ttu-id="e30f9-180">Если `path` указывает на элемент массива, элемент удаляется.</span><span class="sxs-lookup"><span data-stu-id="e30f9-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="e30f9-181">Если `path` указывает на свойство:</span><span class="sxs-lookup"><span data-stu-id="e30f9-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="e30f9-182">Если обновляемый ресурс является динамическим объектом, свойство удаляется.</span><span class="sxs-lookup"><span data-stu-id="e30f9-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="e30f9-183">Если обновляемый ресурс является статическим объектом:</span><span class="sxs-lookup"><span data-stu-id="e30f9-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="e30f9-184">Если свойство может принимать значения NULL, ему присваивается значение NULL.</span><span class="sxs-lookup"><span data-stu-id="e30f9-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="e30f9-185">Если свойство не может принимать значения NULL, ему присваивается значение `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="e30f9-186">В следующем образце документа исправления устанавливается `CustomerName` значение NULL и удаляются `Orders[0]` следующие значения:</span><span class="sxs-lookup"><span data-stu-id="e30f9-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="e30f9-187">Операция замены</span><span class="sxs-lookup"><span data-stu-id="e30f9-187">The replace operation</span></span>

<span data-ttu-id="e30f9-188">Эта операция функционально идентична операции `remove` с последующей `add`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="e30f9-189">В следующем образце документа исправления значение параметра `CustomerName` и заменяется `Orders[0]` новым `Order` объектом:</span><span class="sxs-lookup"><span data-stu-id="e30f9-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="e30f9-190">Операция перемещения</span><span class="sxs-lookup"><span data-stu-id="e30f9-190">The move operation</span></span>

* <span data-ttu-id="e30f9-191">Если `path` указывает на элемент массива, элемент `from` копируется в расположение элемента `path`, а затем выполняется операция `remove` для элемента `from`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="e30f9-192">Если `path` указывает на свойство, значение свойства `from` копируется в свойство `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="e30f9-193">Если `path` указывает на несуществующее свойство:</span><span class="sxs-lookup"><span data-stu-id="e30f9-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="e30f9-194">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e30f9-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="e30f9-195">Если исправляемый ресурс является динамическим объектом, значение `from` копируется в местоположение, указанное параметром `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="e30f9-196">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e30f9-196">The following sample patch document:</span></span>

* <span data-ttu-id="e30f9-197">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e30f9-198">присваивает `Orders[0].OrderName` значение NULL;</span><span class="sxs-lookup"><span data-stu-id="e30f9-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="e30f9-199">перемещает `Orders[1]` в расположение перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="e30f9-200">Операция копирования</span><span class="sxs-lookup"><span data-stu-id="e30f9-200">The copy operation</span></span>

<span data-ttu-id="e30f9-201">Эта операция функционально аналогична операции `move` без последнего шага `remove`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="e30f9-202">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e30f9-202">The following sample patch document:</span></span>

* <span data-ttu-id="e30f9-203">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e30f9-204">вставляет копию `Orders[1]` перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="e30f9-205">Операция тестирования</span><span class="sxs-lookup"><span data-stu-id="e30f9-205">The test operation</span></span>

<span data-ttu-id="e30f9-206">Если значение в расположении, которое задано параметром `path`, отличается от предоставленного в `value` значения, запрос завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e30f9-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="e30f9-207">В этом случае сбой распространяется на весь запрос PATCH, даже если все остальные операции в документе с исправлениями могли быть выполнены успешно.</span><span class="sxs-lookup"><span data-stu-id="e30f9-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="e30f9-208">Операция `test` традиционно используется для предотвращения обновлений при возможных конфликтах параллелизма.</span><span class="sxs-lookup"><span data-stu-id="e30f9-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="e30f9-209">Следующий пример документа с исправлениями не изменяет прежнее значение `CustomerName` (John), так как тест завершается ошибкой:</span><span class="sxs-lookup"><span data-stu-id="e30f9-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="e30f9-210">Получение кода</span><span class="sxs-lookup"><span data-stu-id="e30f9-210">Get the code</span></span>

<span data-ttu-id="e30f9-211">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="e30f9-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="e30f9-212">([Инструкция по скачиванию](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="e30f9-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e30f9-213">Чтобы проверить этот пример, запустите приложение и отправьте HTTP-запросы со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="e30f9-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="e30f9-214">URL-адрес: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="e30f9-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="e30f9-215">Метод HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="e30f9-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="e30f9-216">Заголовок: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="e30f9-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="e30f9-217">Текст: Скопируйте и вставьте один из примеров документа JSON исправления из папки проекта *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e30f9-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e30f9-218">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e30f9-218">Additional resources</span></span>

* [<span data-ttu-id="e30f9-219">Спецификация IETF RFC 5789 для метода PATCH</span><span class="sxs-lookup"><span data-stu-id="e30f9-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="e30f9-220">Спецификация IETF RFC 6902 для JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e30f9-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="e30f9-221">Спецификация IETF RFC 6901 для формата Path в JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e30f9-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="e30f9-222">[Документация по JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="e30f9-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="e30f9-223">Содержит ссылки на ресурсы для создания документов JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e30f9-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="e30f9-224">Исходный код JSON Patch для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e30f9-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e30f9-225">В этой статье описывается обработка запросов JSON Patch в веб-API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e30f9-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="e30f9-226">Метод HTTP-запроса PATCH</span><span class="sxs-lookup"><span data-stu-id="e30f9-226">PATCH HTTP request method</span></span>

<span data-ttu-id="e30f9-227">Методы PUT и [PATCH](https://tools.ietf.org/html/rfc5789) используются для обновления существующего ресурса.</span><span class="sxs-lookup"><span data-stu-id="e30f9-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="e30f9-228">Различие между ними заключается в том, что PUT заменяет весь ресурс, а PATCH указывает только изменения.</span><span class="sxs-lookup"><span data-stu-id="e30f9-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="e30f9-229">JSON PATCH</span><span class="sxs-lookup"><span data-stu-id="e30f9-229">JSON Patch</span></span>

<span data-ttu-id="e30f9-230">Формат [JSON Patch](https://tools.ietf.org/html/rfc6902) используется для указания обновлений, применяемых к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="e30f9-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="e30f9-231">Документ JSON Patch содержит массив *операций*.</span><span class="sxs-lookup"><span data-stu-id="e30f9-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="e30f9-232">Каждая операция обозначает определенный тип изменений, например добавление элемента массива или замену значения свойства.</span><span class="sxs-lookup"><span data-stu-id="e30f9-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="e30f9-233">Так, следующие документы JSON описывают ресурс, сведения JSON Patch для изменения этого ресурса и результат применения операций изменения.</span><span class="sxs-lookup"><span data-stu-id="e30f9-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="e30f9-234">Пример ресурса</span><span class="sxs-lookup"><span data-stu-id="e30f9-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="e30f9-235">Пример JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e30f9-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="e30f9-236">В приведенном выше документе JSON:</span><span class="sxs-lookup"><span data-stu-id="e30f9-236">In the preceding JSON:</span></span>

* <span data-ttu-id="e30f9-237">свойство `op` указывает тип операции;</span><span class="sxs-lookup"><span data-stu-id="e30f9-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="e30f9-238">свойство `path` указывает обновляемый элемент;</span><span class="sxs-lookup"><span data-stu-id="e30f9-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="e30f9-239">свойство `value` предоставляет новое значение.</span><span class="sxs-lookup"><span data-stu-id="e30f9-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="e30f9-240">Ресурс после обновления</span><span class="sxs-lookup"><span data-stu-id="e30f9-240">Resource after patch</span></span>

<span data-ttu-id="e30f9-241">Ниже показан ресурс в том состоянии, которое он принимает после применения приведенного выше документа JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e30f9-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="e30f9-242">Изменения, внесенные при применении к ресурсу документа JSON Patch, являются атомарными, то есть ни одна из операций не будет применена, если любая из них завершится сбоем.</span><span class="sxs-lookup"><span data-stu-id="e30f9-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="e30f9-243">Синтаксис Path</span><span class="sxs-lookup"><span data-stu-id="e30f9-243">Path syntax</span></span>

<span data-ttu-id="e30f9-244">Свойство [Path](https://tools.ietf.org/html/rfc6901) в объекте операции содержит косые черты между уровнями.</span><span class="sxs-lookup"><span data-stu-id="e30f9-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="e30f9-245">Например, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="e30f9-246">Для указания элементов массива используются числовые индексы, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="e30f9-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="e30f9-247">Первый элемент массива `addresses` будет обозначаться как `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="e30f9-248">Чтобы применить `add` для последнего элемента массива, укажите дефис (-) вместо номера индекса: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="e30f9-249">Operations</span><span class="sxs-lookup"><span data-stu-id="e30f9-249">Operations</span></span>

<span data-ttu-id="e30f9-250">В следующей таблице перечислены поддерживаемые операции, которые определены в [спецификации JSON Patch](https://tools.ietf.org/html/rfc6902).</span><span class="sxs-lookup"><span data-stu-id="e30f9-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="e30f9-251">Операция</span><span class="sxs-lookup"><span data-stu-id="e30f9-251">Operation</span></span>  | <span data-ttu-id="e30f9-252">Примечания</span><span class="sxs-lookup"><span data-stu-id="e30f9-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="e30f9-253">Добавляет свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="e30f9-253">Add a property or array element.</span></span> <span data-ttu-id="e30f9-254">Для существующего свойства устанавливает значение.</span><span class="sxs-lookup"><span data-stu-id="e30f9-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="e30f9-255">Удаляет свойство или элемент массива.</span><span class="sxs-lookup"><span data-stu-id="e30f9-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="e30f9-256">Действует так же, как `remove` с последующим `add` в том же расположении.</span><span class="sxs-lookup"><span data-stu-id="e30f9-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="e30f9-257">Действует так же, как `remove` из источника с последующим `add`, в котором указаны место назначения и значение из источника.</span><span class="sxs-lookup"><span data-stu-id="e30f9-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="e30f9-258">Действует так же, как `add`, в котором указаны место назначения и значение из источника.</span><span class="sxs-lookup"><span data-stu-id="e30f9-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="e30f9-259">Возвращает успешный код состояния, если значение `path` совпадает с предоставленным `value`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="e30f9-260">JsonPatch в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e30f9-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="e30f9-261">Реализация ASP.NET Core для JSON Patch предоставляется в пакете NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="e30f9-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="e30f9-262">Этот пакет входит в состав метапакета [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e30f9-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="e30f9-263">Код метода действия</span><span class="sxs-lookup"><span data-stu-id="e30f9-263">Action method code</span></span>

<span data-ttu-id="e30f9-264">В контроллере API есть метод действия для JSON Patch, который:</span><span class="sxs-lookup"><span data-stu-id="e30f9-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="e30f9-265">помечен атрибутом `HttpPatch`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="e30f9-266">принимает `JsonPatchDocument<T>` обычно с указанием `[FromBody]`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="e30f9-267">вызывает `ApplyTo` для целевого документа, чтобы применить изменения.</span><span class="sxs-lookup"><span data-stu-id="e30f9-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="e30f9-268">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="e30f9-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="e30f9-269">Этот код из примера приложения работает со следующей моделью `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="e30f9-270">Этот пример метода действия:</span><span class="sxs-lookup"><span data-stu-id="e30f9-270">The sample action method:</span></span>

* <span data-ttu-id="e30f9-271">Создает документ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="e30f9-272">применяет изменения;</span><span class="sxs-lookup"><span data-stu-id="e30f9-272">Applies the patch.</span></span>
* <span data-ttu-id="e30f9-273">возвращает результат в тексте ответа.</span><span class="sxs-lookup"><span data-stu-id="e30f9-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="e30f9-274">В реальном приложении код будет извлекать данные из хранилища, например из базы данных, и обновлять эту базу данных после применения исправлений.</span><span class="sxs-lookup"><span data-stu-id="e30f9-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="e30f9-275">Состояние модели</span><span class="sxs-lookup"><span data-stu-id="e30f9-275">Model state</span></span>

<span data-ttu-id="e30f9-276">Указанный выше пример метода действия вызывает перегрузку `ApplyTo`, которая принимает состояние модели в качестве одного из параметров.</span><span class="sxs-lookup"><span data-stu-id="e30f9-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="e30f9-277">В этом случае вы получаете в ответах сообщения об ошибках.</span><span class="sxs-lookup"><span data-stu-id="e30f9-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="e30f9-278">В следующем примере показан текст ответа с кодом 400 "Неверный запрос" для операции `test`:</span><span class="sxs-lookup"><span data-stu-id="e30f9-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="e30f9-279">Динамические объекты</span><span class="sxs-lookup"><span data-stu-id="e30f9-279">Dynamic objects</span></span>

<span data-ttu-id="e30f9-280">Следующий пример метода действия демонстрирует, как применить исправление к динамическому объекту.</span><span class="sxs-lookup"><span data-stu-id="e30f9-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="e30f9-281">Операция добавления</span><span class="sxs-lookup"><span data-stu-id="e30f9-281">The add operation</span></span>

* <span data-ttu-id="e30f9-282">Если `path` указывает на элемент массива, новый элемент вставляется перед тем, который указан в параметре `path`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="e30f9-283">Если `path` указывает на свойство, задается значение свойства.</span><span class="sxs-lookup"><span data-stu-id="e30f9-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="e30f9-284">Если `path` указывает на несуществующее расположение:</span><span class="sxs-lookup"><span data-stu-id="e30f9-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="e30f9-285">Если обновляемый ресурс является динамическим объектом, добавляется свойство.</span><span class="sxs-lookup"><span data-stu-id="e30f9-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="e30f9-286">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e30f9-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="e30f9-287">Следующий пример документа с исправлениями устанавливает значение `CustomerName` и добавляет объект `Order` в конец массива `Orders`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="e30f9-288">Операция удаления</span><span class="sxs-lookup"><span data-stu-id="e30f9-288">The remove operation</span></span>

* <span data-ttu-id="e30f9-289">Если `path` указывает на элемент массива, элемент удаляется.</span><span class="sxs-lookup"><span data-stu-id="e30f9-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="e30f9-290">Если `path` указывает на свойство:</span><span class="sxs-lookup"><span data-stu-id="e30f9-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="e30f9-291">Если обновляемый ресурс является динамическим объектом, свойство удаляется.</span><span class="sxs-lookup"><span data-stu-id="e30f9-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="e30f9-292">Если обновляемый ресурс является статическим объектом:</span><span class="sxs-lookup"><span data-stu-id="e30f9-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="e30f9-293">Если свойство может принимать значения NULL, ему присваивается значение NULL.</span><span class="sxs-lookup"><span data-stu-id="e30f9-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="e30f9-294">Если свойство не может принимать значения NULL, ему присваивается значение `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="e30f9-295">Следующий пример документа с исправлениями присваивает `CustomerName` значение NULL и удаляет `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="e30f9-296">Операция замены</span><span class="sxs-lookup"><span data-stu-id="e30f9-296">The replace operation</span></span>

<span data-ttu-id="e30f9-297">Эта операция функционально идентична операции `remove` с последующей `add`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="e30f9-298">Следующий пример документа с исправлениями устанавливает значение `CustomerName` и заменяет `Orders[0]` новым объектом `Order`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="e30f9-299">Операция перемещения</span><span class="sxs-lookup"><span data-stu-id="e30f9-299">The move operation</span></span>

* <span data-ttu-id="e30f9-300">Если `path` указывает на элемент массива, элемент `from` копируется в расположение элемента `path`, а затем выполняется операция `remove` для элемента `from`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="e30f9-301">Если `path` указывает на свойство, значение свойства `from` копируется в свойство `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="e30f9-302">Если `path` указывает на несуществующее свойство:</span><span class="sxs-lookup"><span data-stu-id="e30f9-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="e30f9-303">Если обновляемый ресурс является статическим объектом, запрос завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e30f9-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="e30f9-304">Если исправляемый ресурс является динамическим объектом, значение `from` копируется в местоположение, указанное параметром `path`, а затем выполняется операция `remove` для свойства `from`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="e30f9-305">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e30f9-305">The following sample patch document:</span></span>

* <span data-ttu-id="e30f9-306">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e30f9-307">присваивает `Orders[0].OrderName` значение NULL;</span><span class="sxs-lookup"><span data-stu-id="e30f9-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="e30f9-308">перемещает `Orders[1]` в расположение перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="e30f9-309">Операция копирования</span><span class="sxs-lookup"><span data-stu-id="e30f9-309">The copy operation</span></span>

<span data-ttu-id="e30f9-310">Эта операция функционально аналогична операции `move` без последнего шага `remove`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="e30f9-311">Следующий пример документа исправления выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="e30f9-311">The following sample patch document:</span></span>

* <span data-ttu-id="e30f9-312">копирует значение `Orders[0].OrderName` в `CustomerName`;</span><span class="sxs-lookup"><span data-stu-id="e30f9-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="e30f9-313">вставляет копию `Orders[1]` перед `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="e30f9-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="e30f9-314">Операция тестирования</span><span class="sxs-lookup"><span data-stu-id="e30f9-314">The test operation</span></span>

<span data-ttu-id="e30f9-315">Если значение в расположении, которое задано параметром `path`, отличается от предоставленного в `value` значения, запрос завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="e30f9-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="e30f9-316">В этом случае сбой распространяется на весь запрос PATCH, даже если все остальные операции в документе с исправлениями могли быть выполнены успешно.</span><span class="sxs-lookup"><span data-stu-id="e30f9-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="e30f9-317">Операция `test` традиционно используется для предотвращения обновлений при возможных конфликтах параллелизма.</span><span class="sxs-lookup"><span data-stu-id="e30f9-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="e30f9-318">Следующий пример документа с исправлениями не изменяет прежнее значение `CustomerName` (John), так как тест завершается ошибкой:</span><span class="sxs-lookup"><span data-stu-id="e30f9-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="e30f9-319">Получение кода</span><span class="sxs-lookup"><span data-stu-id="e30f9-319">Get the code</span></span>

<span data-ttu-id="e30f9-320">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="e30f9-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="e30f9-321">([Инструкция по скачиванию](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="e30f9-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="e30f9-322">Чтобы проверить этот пример, запустите приложение и отправьте HTTP-запросы со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="e30f9-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="e30f9-323">URL-адрес: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="e30f9-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="e30f9-324">Метод HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="e30f9-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="e30f9-325">Заголовок: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="e30f9-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="e30f9-326">Текст: Скопируйте и вставьте один из примеров документа JSON исправления из папки проекта *JSON* .</span><span class="sxs-lookup"><span data-stu-id="e30f9-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e30f9-327">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e30f9-327">Additional resources</span></span>

* [<span data-ttu-id="e30f9-328">Спецификация IETF RFC 5789 для метода PATCH</span><span class="sxs-lookup"><span data-stu-id="e30f9-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="e30f9-329">Спецификация IETF RFC 6902 для JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e30f9-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="e30f9-330">Спецификация IETF RFC 6901 для формата Path в JSON Patch</span><span class="sxs-lookup"><span data-stu-id="e30f9-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="e30f9-331">[Документация по JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="e30f9-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="e30f9-332">Содержит ссылки на ресурсы для создания документов JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="e30f9-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="e30f9-333">Исходный код JSON Patch для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e30f9-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end
