---
title: Привязка модели в ASP.NET Core
author: rick-anderson
description: Узнайте, как работает привязка модели в ASP.NET Core и как настроить ее поведение.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 5eaedf6dbe5df59848b9cf8a5bda67add48db2a6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586947"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="546fd-103">Привязка модели в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="546fd-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="546fd-104">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="546fd-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="546fd-105">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="546fd-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="546fd-106">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="546fd-106">What is Model binding</span></span>

<span data-ttu-id="546fd-107">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="546fd-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="546fd-108">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="546fd-109">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="546fd-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="546fd-110">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="546fd-110">Model binding automates this process.</span></span> <span data-ttu-id="546fd-111">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="546fd-111">The model binding system:</span></span>

* <span data-ttu-id="546fd-112">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="546fd-113">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="546fd-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="546fd-114">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="546fd-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="546fd-115">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="546fd-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="546fd-116">Например, .</span><span class="sxs-lookup"><span data-stu-id="546fd-116">Example</span></span>

<span data-ttu-id="546fd-117">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="546fd-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="546fd-118">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="546fd-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="546fd-119">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="546fd-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="546fd-120">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="546fd-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="546fd-121">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="546fd-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="546fd-122">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="546fd-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="546fd-123">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="546fd-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="546fd-124">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="546fd-125">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="546fd-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="546fd-126">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="546fd-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="546fd-127">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="546fd-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="546fd-128">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="546fd-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="546fd-129">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="546fd-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="546fd-130">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="546fd-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="546fd-131">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="546fd-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="546fd-132">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="546fd-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="546fd-133">Цели</span><span class="sxs-lookup"><span data-stu-id="546fd-133">Targets</span></span>

<span data-ttu-id="546fd-134">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="546fd-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="546fd-135">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="546fd-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="546fd-136">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="546fd-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="546fd-137">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="546fd-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="546fd-138">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="546fd-138">[BindProperty] attribute</span></span>

<span data-ttu-id="546fd-139">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="546fd-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="546fd-140">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="546fd-140">[BindProperties] attribute</span></span>

<span data-ttu-id="546fd-141">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="546fd-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="546fd-142">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="546fd-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="546fd-143">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="546fd-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="546fd-144">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="546fd-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="546fd-145">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="546fd-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="546fd-146">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="546fd-147">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="546fd-148">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="546fd-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="546fd-149">Источники</span><span class="sxs-lookup"><span data-stu-id="546fd-149">Sources</span></span>

<span data-ttu-id="546fd-150">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="546fd-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="546fd-151">Поля формы</span><span class="sxs-lookup"><span data-stu-id="546fd-151">Form fields</span></span>
1. <span data-ttu-id="546fd-152">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="546fd-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="546fd-153">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="546fd-153">Route data</span></span>
1. <span data-ttu-id="546fd-154">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="546fd-154">Query string parameters</span></span>
1. <span data-ttu-id="546fd-155">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="546fd-155">Uploaded files</span></span>

<span data-ttu-id="546fd-156">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="546fd-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="546fd-157">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="546fd-157">There are a few exceptions:</span></span>

* <span data-ttu-id="546fd-158">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="546fd-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="546fd-159">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="546fd-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="546fd-160">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="546fd-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="546fd-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="546fd-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="546fd-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="546fd-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="546fd-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="546fd-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="546fd-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="546fd-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="546fd-166">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="546fd-166">These attributes:</span></span>

* <span data-ttu-id="546fd-167">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="546fd-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="546fd-168">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="546fd-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="546fd-169">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="546fd-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="546fd-170">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="546fd-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="546fd-171">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="546fd-171">[FromBody] attribute</span></span>

<span data-ttu-id="546fd-172">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="546fd-173">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="546fd-174">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="546fd-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="546fd-175">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="546fd-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="546fd-176">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="546fd-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="546fd-177">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="546fd-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="546fd-178">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="546fd-178">In the preceding example:</span></span>

* <span data-ttu-id="546fd-179">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="546fd-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="546fd-180">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="546fd-181">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="546fd-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="546fd-182">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="546fd-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="546fd-183">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="546fd-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="546fd-184">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="546fd-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="546fd-185">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="546fd-185">Additional sources</span></span>

<span data-ttu-id="546fd-186">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="546fd-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="546fd-187">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="546fd-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="546fd-188">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="546fd-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="546fd-189">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="546fd-189">To get data from a new source:</span></span>

* <span data-ttu-id="546fd-190">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="546fd-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="546fd-191">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="546fd-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="546fd-192">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="546fd-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="546fd-193">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="546fd-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="546fd-194">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="546fd-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="546fd-195">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="546fd-196">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="546fd-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="546fd-197">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="546fd-197">No source for a model property</span></span>

<span data-ttu-id="546fd-198">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="546fd-199">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="546fd-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="546fd-200">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="546fd-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="546fd-201">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="546fd-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="546fd-202">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="546fd-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="546fd-203">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="546fd-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="546fd-204">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="546fd-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="546fd-205">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="546fd-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="546fd-206">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="546fd-207">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="546fd-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="546fd-208">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="546fd-208">Type conversion errors</span></span>

<span data-ttu-id="546fd-209">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="546fd-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="546fd-210">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="546fd-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="546fd-211">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="546fd-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="546fd-212">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="546fd-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="546fd-213">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="546fd-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="546fd-214">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="546fd-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="546fd-215">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="546fd-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="546fd-216">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="546fd-217">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="546fd-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="546fd-218">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="546fd-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="546fd-219">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="546fd-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="546fd-220">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="546fd-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="546fd-221">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="546fd-222">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="546fd-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="546fd-223">Простые типы</span><span class="sxs-lookup"><span data-stu-id="546fd-223">Simple types</span></span>

<span data-ttu-id="546fd-224">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="546fd-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="546fd-225">Логический</span><span class="sxs-lookup"><span data-stu-id="546fd-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="546fd-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="546fd-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="546fd-227">Char</span><span class="sxs-lookup"><span data-stu-id="546fd-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="546fd-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="546fd-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="546fd-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="546fd-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="546fd-230">Десятичное число</span><span class="sxs-lookup"><span data-stu-id="546fd-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="546fd-231">Double</span><span class="sxs-lookup"><span data-stu-id="546fd-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="546fd-232">Enum</span><span class="sxs-lookup"><span data-stu-id="546fd-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="546fd-233">Guid</span><span class="sxs-lookup"><span data-stu-id="546fd-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="546fd-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="546fd-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="546fd-235">Single</span><span class="sxs-lookup"><span data-stu-id="546fd-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="546fd-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="546fd-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="546fd-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="546fd-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="546fd-238">URI</span><span class="sxs-lookup"><span data-stu-id="546fd-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="546fd-239">Версия</span><span class="sxs-lookup"><span data-stu-id="546fd-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="546fd-240">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="546fd-240">Complex types</span></span>

<span data-ttu-id="546fd-241">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="546fd-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="546fd-242">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="546fd-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="546fd-243">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="546fd-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="546fd-244">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="546fd-245">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="546fd-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="546fd-246">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="546fd-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="546fd-247">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="546fd-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="546fd-248">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="546fd-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="546fd-249">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="546fd-249">Prefix = parameter name</span></span>

<span data-ttu-id="546fd-250">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="546fd-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="546fd-251">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="546fd-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="546fd-252">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="546fd-253">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="546fd-253">Prefix = property name</span></span>

<span data-ttu-id="546fd-254">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="546fd-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="546fd-255">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="546fd-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="546fd-256">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="546fd-257">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="546fd-257">Custom prefix</span></span>

<span data-ttu-id="546fd-258">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="546fd-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="546fd-259">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="546fd-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="546fd-260">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="546fd-261">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="546fd-261">Attributes for complex type targets</span></span>

<span data-ttu-id="546fd-262">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="546fd-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="546fd-263">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="546fd-264">Они ***не*** влияют на модули форматирования ввода, которые обрабатывают опубликованные тексты JSON и XML-запросов.</span><span class="sxs-lookup"><span data-stu-id="546fd-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="546fd-265">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="546fd-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="546fd-266">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="546fd-266">[Bind] attribute</span></span>

<span data-ttu-id="546fd-267">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="546fd-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="546fd-268">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="546fd-269">`[Bind]` не ***влияет на*** модули форматирования ввода.</span><span class="sxs-lookup"><span data-stu-id="546fd-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="546fd-270">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="546fd-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="546fd-271">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="546fd-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="546fd-272">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="546fd-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="546fd-273">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="546fd-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="546fd-274">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="546fd-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="546fd-275">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="546fd-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="546fd-276">Атрибут [Моделбиндер]</span><span class="sxs-lookup"><span data-stu-id="546fd-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="546fd-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> может применяться к типам, свойствам или параметрам.</span><span class="sxs-lookup"><span data-stu-id="546fd-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="546fd-278">Он позволяет указать тип связывателя модели, используемый для привязки конкретного экземпляра или типа.</span><span class="sxs-lookup"><span data-stu-id="546fd-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="546fd-279">Пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="546fd-280">`[ModelBinder]`Атрибут также можно использовать для изменения имени свойства или параметра, если оно привязано к модели:</span><span class="sxs-lookup"><span data-stu-id="546fd-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="546fd-281">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="546fd-281">[BindRequired] attribute</span></span>

<span data-ttu-id="546fd-282">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="546fd-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="546fd-283">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="546fd-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="546fd-284">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="546fd-285">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="546fd-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="546fd-286">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="546fd-286">[BindNever] attribute</span></span>

<span data-ttu-id="546fd-287">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="546fd-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="546fd-288">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="546fd-289">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="546fd-290">Коллекции</span><span class="sxs-lookup"><span data-stu-id="546fd-290">Collections</span></span>

<span data-ttu-id="546fd-291">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="546fd-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="546fd-292">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="546fd-293">Пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-293">For example:</span></span>

* <span data-ttu-id="546fd-294">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="546fd-295">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="546fd-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="546fd-296">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="546fd-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="546fd-297">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="546fd-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="546fd-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="546fd-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="546fd-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="546fd-300">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="546fd-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="546fd-301">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="546fd-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="546fd-302">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="546fd-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="546fd-303">Словари</span><span class="sxs-lookup"><span data-stu-id="546fd-303">Dictionaries</span></span>

<span data-ttu-id="546fd-304">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="546fd-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="546fd-305">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="546fd-306">Пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-306">For example:</span></span>

* <span data-ttu-id="546fd-307">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="546fd-308">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="546fd-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="546fd-309">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="546fd-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="546fd-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="546fd-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="546fd-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="546fd-312">Привязка конструктора и типы записей</span><span class="sxs-lookup"><span data-stu-id="546fd-312">Constructor binding and record types</span></span>

<span data-ttu-id="546fd-313">Привязка модели требует, чтобы сложные типы имели конструктор без параметров.</span><span class="sxs-lookup"><span data-stu-id="546fd-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="546fd-314">`System.Text.Json` `Newtonsoft.Json` Модули форматирования ввода и поддерживают десериализацию классов, у которых нет конструктора без параметров.</span><span class="sxs-lookup"><span data-stu-id="546fd-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="546fd-315">В C# 9 появились типы записей, которые представляют собой отличный способ краткого представления данных по сети.</span><span class="sxs-lookup"><span data-stu-id="546fd-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="546fd-316">ASP.NET Core добавляет поддержку привязки модели и проверку типов записей с помощью одного конструктора:</span><span class="sxs-lookup"><span data-stu-id="546fd-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="546fd-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="546fd-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="546fd-318">При проверке типов записей среда выполнения ищет метаданные проверки специально для параметров, а не для свойств.</span><span class="sxs-lookup"><span data-stu-id="546fd-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="546fd-319">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="546fd-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="546fd-320">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="546fd-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="546fd-321">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="546fd-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="546fd-322">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="546fd-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="546fd-323">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="546fd-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="546fd-324">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="546fd-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="546fd-325">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="546fd-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="546fd-326">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="546fd-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="546fd-327">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="546fd-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="546fd-328">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="546fd-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="546fd-329">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="546fd-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="546fd-330">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="546fd-330">Special data types</span></span>

<span data-ttu-id="546fd-331">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="546fd-332">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="546fd-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="546fd-333">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="546fd-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="546fd-334">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="546fd-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="546fd-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="546fd-335">CancellationToken</span></span>

<span data-ttu-id="546fd-336">При необходимости действия могут быть привязаны `CancellationToken` как параметр.</span><span class="sxs-lookup"><span data-stu-id="546fd-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="546fd-337">Это привязывает этот <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> сигнал при прерывании соединения, лежащего в основе HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="546fd-338">Действия могут использовать этот параметр для отмены долго выполняющихся асинхронных операций, выполняемых в рамках действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="546fd-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="546fd-339">FormCollection</span><span class="sxs-lookup"><span data-stu-id="546fd-339">FormCollection</span></span>

<span data-ttu-id="546fd-340">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="546fd-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="546fd-341">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="546fd-341">Input formatters</span></span>

<span data-ttu-id="546fd-342">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="546fd-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="546fd-343">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="546fd-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="546fd-344">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="546fd-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="546fd-345">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="546fd-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="546fd-346">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="546fd-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="546fd-347">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="546fd-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="546fd-348">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="546fd-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="546fd-349">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="546fd-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="546fd-350">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="546fd-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="546fd-351">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="546fd-352">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="546fd-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="546fd-353">Настройка привязки модели с помощью форматировщиков входных данных</span><span class="sxs-lookup"><span data-stu-id="546fd-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="546fd-354">Форматировщик входных данных полностью отвечает за чтение данных из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="546fd-355">Чтобы настроить этот процесс, настройте API-интерфейсы, используемые форматировщиками входных данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="546fd-356">В этом разделе описывается, как настроить форматировщик входных данных на основе `System.Text.Json` так, чтобы он понимал настраиваемый тип с именем `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="546fd-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="546fd-357">Рассмотрим следующую модель, которая содержит настраиваемое свойство `ObjectId` с именем `Id`:</span><span class="sxs-lookup"><span data-stu-id="546fd-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="546fd-358">Чтобы настроить процесс привязки модели при использовании `System.Text.Json`, создайте производный класс на основе класса <xref:System.Text.Json.Serialization.JsonConverter%601>:</span><span class="sxs-lookup"><span data-stu-id="546fd-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="546fd-359">Чтобы использовать пользовательский преобразователь, примените к типу атрибут <xref:System.Text.Json.Serialization.JsonConverterAttribute>.</span><span class="sxs-lookup"><span data-stu-id="546fd-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="546fd-360">В следующем примере тип `ObjectId` настраивается с `ObjectIdConverter` в качестве пользовательского преобразователя:</span><span class="sxs-lookup"><span data-stu-id="546fd-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="546fd-361">Дополнительные сведения см. в статье [How to write custom converters for JSON serialization (marshalling) in .NET](/dotnet/standard/serialization/system-text-json-converters-how-to) (Создание пользовательских преобразователей для сериализации JSON (маршалинг) в .NET).</span><span class="sxs-lookup"><span data-stu-id="546fd-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="546fd-362">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="546fd-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="546fd-363">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="546fd-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="546fd-364">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="546fd-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="546fd-365">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="546fd-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="546fd-366">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="546fd-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="546fd-367">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="546fd-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="546fd-368">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="546fd-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="546fd-369">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="546fd-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="546fd-370">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="546fd-370">Custom model binders</span></span>

<span data-ttu-id="546fd-371">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="546fd-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="546fd-372">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="546fd-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="546fd-373">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="546fd-373">Manual model binding</span></span> 

<span data-ttu-id="546fd-374">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="546fd-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="546fd-375">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="546fd-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="546fd-376">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="546fd-377">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="546fd-378">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="546fd-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> использует поставщиков значений для получения данных из текста формы, строки запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="546fd-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="546fd-380">`TryUpdateModelAsync` как правило:</span><span class="sxs-lookup"><span data-stu-id="546fd-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="546fd-381">Используется с Razor страницами и приложениями MVC с использованием контроллеров и представлений для предотвращения перебора.</span><span class="sxs-lookup"><span data-stu-id="546fd-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="546fd-382">не используется с веб-API, если только не используется из данных формы, строк запроса и данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="546fd-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="546fd-383">Конечные точки веб-API, которые используют JSON, применяют [форматировщики входных данных](#input-formatters) для десериализации текста запроса в объект.</span><span class="sxs-lookup"><span data-stu-id="546fd-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="546fd-384">Дополнительные сведения см. в разделе [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="546fd-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="546fd-385">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="546fd-385">[FromServices] attribute</span></span>

<span data-ttu-id="546fd-386">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="546fd-387">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="546fd-388">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="546fd-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="546fd-389">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="546fd-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="546fd-390">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="546fd-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="546fd-391">В этой статье объясняется, что такое привязка модели, как это работает и как настроить ее поведение.</span><span class="sxs-lookup"><span data-stu-id="546fd-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="546fd-392">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="546fd-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="546fd-393">Что такое привязка модели</span><span class="sxs-lookup"><span data-stu-id="546fd-393">What is Model binding</span></span>

<span data-ttu-id="546fd-394">Контроллеры и Razor страницы работают с данными, поступающими из HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="546fd-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="546fd-395">Например, данные о маршруте могут предоставлять ключ записи, а опубликованные поля формы могут предоставлять значения для свойств модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="546fd-396">Написание кода для получения этих значений и их преобразования из строк в типы .NET будет утомительной задачей с высоким риском ошибок.</span><span class="sxs-lookup"><span data-stu-id="546fd-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="546fd-397">Привязка модели позволяет автоматизировать этот процесс.</span><span class="sxs-lookup"><span data-stu-id="546fd-397">Model binding automates this process.</span></span> <span data-ttu-id="546fd-398">Система привязки модели:</span><span class="sxs-lookup"><span data-stu-id="546fd-398">The model binding system:</span></span>

* <span data-ttu-id="546fd-399">Извлекает данные из различных источников, таких как данные о маршруте, поля формы и строки запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="546fd-400">Предоставляет данные для контроллеров и Razor страниц в параметрах метода и общих свойствах.</span><span class="sxs-lookup"><span data-stu-id="546fd-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="546fd-401">Преобразует строковые данные в типы .NET.</span><span class="sxs-lookup"><span data-stu-id="546fd-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="546fd-402">Обновляет свойства сложных типов.</span><span class="sxs-lookup"><span data-stu-id="546fd-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="546fd-403">Например, .</span><span class="sxs-lookup"><span data-stu-id="546fd-403">Example</span></span>

<span data-ttu-id="546fd-404">Предположим, у вас есть следующий метод действия:</span><span class="sxs-lookup"><span data-stu-id="546fd-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="546fd-405">И приложение получает запрос с этим URL-адресом:</span><span class="sxs-lookup"><span data-stu-id="546fd-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="546fd-406">Привязка модели выполняет следующие действия, после того, как система маршрутизации выберет метод действия:</span><span class="sxs-lookup"><span data-stu-id="546fd-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="546fd-407">Находит первый параметр `GetByID`, целое число с именем `id`.</span><span class="sxs-lookup"><span data-stu-id="546fd-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="546fd-408">Просматривает доступные источники в HTTP-запросе и находит `id` = "2" в данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="546fd-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="546fd-409">Преобразует строку "2" в целое число 2.</span><span class="sxs-lookup"><span data-stu-id="546fd-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="546fd-410">Находит следующий параметр `GetByID`, логическое значение с именем `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="546fd-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="546fd-411">Просматривает источники и находит "DogsOnly=true" в строке запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="546fd-412">Сопоставление имен не учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="546fd-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="546fd-413">Преобразует строку "true" в логическое значение `true`.</span><span class="sxs-lookup"><span data-stu-id="546fd-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="546fd-414">Затем платформа вызывает метод `GetById`, передавая 2 для параметра `id` и `true` для параметра `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="546fd-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="546fd-415">В приведенном выше примере целевые объекты привязки модели — это параметры методов, которые являются примитивными типами.</span><span class="sxs-lookup"><span data-stu-id="546fd-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="546fd-416">Целевые объекты также могут быть свойствами сложного типа.</span><span class="sxs-lookup"><span data-stu-id="546fd-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="546fd-417">После успешной привязки каждого свойства осуществляется [проверка модели](xref:mvc/models/validation) для этого свойства.</span><span class="sxs-lookup"><span data-stu-id="546fd-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="546fd-418">Записи о данных, привязанных к модели, а также об ошибках привязки или проверки хранятся в [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) или [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span><span class="sxs-lookup"><span data-stu-id="546fd-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="546fd-419">Чтобы узнать об успешном выполнении этого процесса, приложение проверяет наличие флага [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="546fd-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="546fd-420">Цели</span><span class="sxs-lookup"><span data-stu-id="546fd-420">Targets</span></span>

<span data-ttu-id="546fd-421">Привязка модели попытается найти значения для следующих типов целевых объектов:</span><span class="sxs-lookup"><span data-stu-id="546fd-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="546fd-422">Параметры метода действия контроллера, к которому направлен запрос.</span><span class="sxs-lookup"><span data-stu-id="546fd-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="546fd-423">Параметры Razor метода обработчика страниц, к которому направляется запрос.</span><span class="sxs-lookup"><span data-stu-id="546fd-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="546fd-424">Открытые свойства контроллера или класса `PageModel`, если задано атрибутами.</span><span class="sxs-lookup"><span data-stu-id="546fd-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="546fd-425">Атрибут [BindProperty]</span><span class="sxs-lookup"><span data-stu-id="546fd-425">[BindProperty] attribute</span></span>

<span data-ttu-id="546fd-426">Может применяться к открытому свойству контроллера или класса `PageModel` для привязки модели для этого свойства:</span><span class="sxs-lookup"><span data-stu-id="546fd-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="546fd-427">Атрибут [BindProperties]</span><span class="sxs-lookup"><span data-stu-id="546fd-427">[BindProperties] attribute</span></span>

<span data-ttu-id="546fd-428">Доступно в ASP.NET Core 2.1 и более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="546fd-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="546fd-429">Может применяться к контроллеру или классу `PageModel`, чтобы привязка модели была направлена на все открытые свойства этого класса:</span><span class="sxs-lookup"><span data-stu-id="546fd-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="546fd-430">Привязка модели для HTTP-запросов GET</span><span class="sxs-lookup"><span data-stu-id="546fd-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="546fd-431">По умолчанию свойства не привязываются к HTTP-запросам GET.</span><span class="sxs-lookup"><span data-stu-id="546fd-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="546fd-432">Как правило, для запроса GET вам нужен только параметр идентификатора записи.</span><span class="sxs-lookup"><span data-stu-id="546fd-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="546fd-433">Идентификатор записи используется для поиска элемента в базе данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="546fd-434">Поэтому не нужно привязывать свойство, которое содержит экземпляр модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="546fd-435">Если вы хотите привязать свойства к данным от запросов GET, задайте для свойства `SupportsGet` значение `true`:</span><span class="sxs-lookup"><span data-stu-id="546fd-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="546fd-436">Источники</span><span class="sxs-lookup"><span data-stu-id="546fd-436">Sources</span></span>

<span data-ttu-id="546fd-437">По умолчанию привязка модели получает данные в виде пар "ключ-значение" из следующих источников в HTTP-запросе:</span><span class="sxs-lookup"><span data-stu-id="546fd-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="546fd-438">Поля формы</span><span class="sxs-lookup"><span data-stu-id="546fd-438">Form fields</span></span>
1. <span data-ttu-id="546fd-439">Текст запроса (для [контроллеров, имеющих атрибут [ApiController]](xref:web-api/index#binding-source-parameter-inference).)</span><span class="sxs-lookup"><span data-stu-id="546fd-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="546fd-440">Данные маршрута</span><span class="sxs-lookup"><span data-stu-id="546fd-440">Route data</span></span>
1. <span data-ttu-id="546fd-441">Параметры строки запроса</span><span class="sxs-lookup"><span data-stu-id="546fd-441">Query string parameters</span></span>
1. <span data-ttu-id="546fd-442">Отправленные файлы</span><span class="sxs-lookup"><span data-stu-id="546fd-442">Uploaded files</span></span>

<span data-ttu-id="546fd-443">Для каждого целевого параметра или свойства источники проверяются в порядке, указанном в предыдущем списке.</span><span class="sxs-lookup"><span data-stu-id="546fd-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="546fd-444">Существует несколько исключений:</span><span class="sxs-lookup"><span data-stu-id="546fd-444">There are a few exceptions:</span></span>

* <span data-ttu-id="546fd-445">Данные маршрутизации и значения строк запросов используются только для примитивных типов.</span><span class="sxs-lookup"><span data-stu-id="546fd-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="546fd-446">Отправленные файлы привязаны только к типам целевых объектов, которые реализуют `IFormFile` или `IEnumerable<IFormFile>`.</span><span class="sxs-lookup"><span data-stu-id="546fd-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="546fd-447">Если источник по умолчанию неверен, используйте один из следующих атрибутов, чтобы указать источник:</span><span class="sxs-lookup"><span data-stu-id="546fd-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="546fd-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) — Получает значения из строки запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="546fd-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) — Получает значения из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="546fd-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="546fd-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) — Получает значения из опубликованных полей формы.</span><span class="sxs-lookup"><span data-stu-id="546fd-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="546fd-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) — Получает значения из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="546fd-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) — Получает значения из заголовков HTTP.</span><span class="sxs-lookup"><span data-stu-id="546fd-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="546fd-453">Эти атрибуты:</span><span class="sxs-lookup"><span data-stu-id="546fd-453">These attributes:</span></span>

* <span data-ttu-id="546fd-454">Добавляются к свойствам модели по отдельности (не к классу модели), как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="546fd-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="546fd-455">При необходимости принимают значение имени модели в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="546fd-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="546fd-456">Этот параметр предоставляется в том случае, если имя свойства не соответствует значению в запросе.</span><span class="sxs-lookup"><span data-stu-id="546fd-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="546fd-457">Например, значение в запросе может быть заголовком с дефисом в имени, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="546fd-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="546fd-458">Атрибут [FromBody]</span><span class="sxs-lookup"><span data-stu-id="546fd-458">[FromBody] attribute</span></span>

<span data-ttu-id="546fd-459">Примените атрибут `[FromBody]` к параметру, чтобы заполнить его свойства из тела HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="546fd-460">Среда выполнения ASP.NET Core делегирует ответственность за считывание тела форматировщику входных данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="546fd-461">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="546fd-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="546fd-462">При применении `[FromBody]` к параметру сложного типа все атрибуты источника привязки, применяемые к его свойствам, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="546fd-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="546fd-463">Например, следующее действие `Create` указывает, что параметр `pet` заполняется из тела:</span><span class="sxs-lookup"><span data-stu-id="546fd-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="546fd-464">Класс `Pet` указывает, что свойство `Breed` заполняется из параметра строки запроса:</span><span class="sxs-lookup"><span data-stu-id="546fd-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="546fd-465">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="546fd-465">In the preceding example:</span></span>

* <span data-ttu-id="546fd-466">Атрибут `[FromQuery]` не учитывается.</span><span class="sxs-lookup"><span data-stu-id="546fd-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="546fd-467">Свойство `Breed` не заполняется из параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="546fd-468">Форматировщики входных данных считывают только тело и не распознают атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="546fd-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="546fd-469">Если подходящее значение найдено в теле, оно используется для заполнения свойства `Breed`.</span><span class="sxs-lookup"><span data-stu-id="546fd-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="546fd-470">Не применяют `[FromBody]` к нескольким параметрам в методе действия.</span><span class="sxs-lookup"><span data-stu-id="546fd-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="546fd-471">После считывания потока запроса форматировщиком входных данных он больше не доступен для повторного чтения для привязки других параметров `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="546fd-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="546fd-472">Дополнительные источники</span><span class="sxs-lookup"><span data-stu-id="546fd-472">Additional sources</span></span>

<span data-ttu-id="546fd-473">Исходные данные предоставляются системой привязки модели *поставщиками значений*.</span><span class="sxs-lookup"><span data-stu-id="546fd-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="546fd-474">Вы можете записать и зарегистрировать пользовательские поставщики значений, которые получают данные для привязки модели из других источников.</span><span class="sxs-lookup"><span data-stu-id="546fd-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="546fd-475">Например, могут потребоваться данные из cookie s или из состояния сеанса.</span><span class="sxs-lookup"><span data-stu-id="546fd-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="546fd-476">Для получения данных из нового источника:</span><span class="sxs-lookup"><span data-stu-id="546fd-476">To get data from a new source:</span></span>

* <span data-ttu-id="546fd-477">Создайте класс, реализующий `IValueProvider`.</span><span class="sxs-lookup"><span data-stu-id="546fd-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="546fd-478">Создайте класс, реализующий `IValueProviderFactory`.</span><span class="sxs-lookup"><span data-stu-id="546fd-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="546fd-479">Зарегистрируйте класс фабрики в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="546fd-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="546fd-480">Пример приложения включает в себя [поставщик значений](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) и пример [фабрики](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) , который получает значения из cookie s.</span><span class="sxs-lookup"><span data-stu-id="546fd-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="546fd-481">Ниже приведен код регистрации в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="546fd-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="546fd-482">Этот код помещает поставщик пользовательских значений после всех встроенных поставщиков значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="546fd-483">Чтобы сделать его первым в списке, вызовите `Insert(0, new CookieValueProviderFactory())` вместо `Add`.</span><span class="sxs-lookup"><span data-stu-id="546fd-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="546fd-484">Отсутствие источника для свойства модели</span><span class="sxs-lookup"><span data-stu-id="546fd-484">No source for a model property</span></span>

<span data-ttu-id="546fd-485">По умолчанию ошибка состояния модели не создается, если не найдено значение для свойства модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="546fd-486">Свойство получает значение NULL или значение по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="546fd-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="546fd-487">Примитивным типам, допускающим значение NULL, задается значение `null`.</span><span class="sxs-lookup"><span data-stu-id="546fd-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="546fd-488">Типам значений, не допускающим значение NULL, задается значение `default(T)`.</span><span class="sxs-lookup"><span data-stu-id="546fd-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="546fd-489">Например, параметр `int id` получает значение 0.</span><span class="sxs-lookup"><span data-stu-id="546fd-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="546fd-490">Для сложных типов привязка модели создает экземпляр с помощью конструктора по умолчанию без задания свойств.</span><span class="sxs-lookup"><span data-stu-id="546fd-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="546fd-491">Массивы имеют значение `Array.Empty<T>()`, за исключением массивов `byte[]`, которые имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="546fd-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="546fd-492">Если состояние модели должно быть недействительным, если в полях формы для свойства модели не найдено ничего, используйте [`[BindRequired]`](#bindrequired-attribute) атрибут.</span><span class="sxs-lookup"><span data-stu-id="546fd-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="546fd-493">Обратите внимание, это поведение `[BindRequired]` применяется к привязке модели из опубликованных данных формы, а не к данным JSON или XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="546fd-494">Данные основного текста запроса обрабатываются [форматировщиками входных данных](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="546fd-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="546fd-495">Ошибки преобразования типа</span><span class="sxs-lookup"><span data-stu-id="546fd-495">Type conversion errors</span></span>

<span data-ttu-id="546fd-496">Если источник найден, но его нельзя преобразовать в тип целевого объекта, состояние модели помечается как недопустимое.</span><span class="sxs-lookup"><span data-stu-id="546fd-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="546fd-497">Параметр или свойство целевого объекта получает значение NULL или значение по умолчанию, как отмечалось в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="546fd-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="546fd-498">В контроллере API с атрибутом `[ApiController]` недопустимое состояние модели приводит к автоматическому ответу HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="546fd-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="546fd-499">На Razor странице повторно отобразите страницу с сообщением об ошибке:</span><span class="sxs-lookup"><span data-stu-id="546fd-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="546fd-500">Проверка на стороне клиента приводит к перехвату большинства недопустимых данных, которые в противном случае были бы переданы в Razor форму страниц.</span><span class="sxs-lookup"><span data-stu-id="546fd-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="546fd-501">Эта проверка затрудняет срабатывание выделенного выше кода.</span><span class="sxs-lookup"><span data-stu-id="546fd-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="546fd-502">Пример приложения включает кнопку **Отправить с неверной датой**, которая помещает недопустимые данные в поле **Дата приема на работу** и отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="546fd-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="546fd-503">Эта кнопка показывает, как работает код для повторного отображения страницы, если возникла ошибка преобразования данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="546fd-504">Когда страница отображается повторно приведенным выше кодом, недопустимые входные данные не отображаются в поле формы.</span><span class="sxs-lookup"><span data-stu-id="546fd-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="546fd-505">Это связано с тем, что свойству модели задано значение NULL или значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="546fd-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="546fd-506">Недопустимые входные данные отображаются в сообщении об ошибке.</span><span class="sxs-lookup"><span data-stu-id="546fd-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="546fd-507">Но если требуется повторно отобразить неправильные данные в поле формы, возможно, следует сделать свойство модели строкой и выполнить преобразование данных вручную.</span><span class="sxs-lookup"><span data-stu-id="546fd-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="546fd-508">Та же стратегия рекомендуется в том случае, если вы не хотите, чтобы ошибки преобразования типов приводили к ошибкам состояния модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="546fd-509">В этом случае следует сделать свойство модели строкой.</span><span class="sxs-lookup"><span data-stu-id="546fd-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="546fd-510">Простые типы</span><span class="sxs-lookup"><span data-stu-id="546fd-510">Simple types</span></span>

<span data-ttu-id="546fd-511">Связыватель модели может преобразовать исходные строки в следующие примитивные типы:</span><span class="sxs-lookup"><span data-stu-id="546fd-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="546fd-512">Логический</span><span class="sxs-lookup"><span data-stu-id="546fd-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="546fd-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="546fd-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="546fd-514">Char</span><span class="sxs-lookup"><span data-stu-id="546fd-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="546fd-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="546fd-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="546fd-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="546fd-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="546fd-517">Десятичное число</span><span class="sxs-lookup"><span data-stu-id="546fd-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="546fd-518">Double</span><span class="sxs-lookup"><span data-stu-id="546fd-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="546fd-519">Enum</span><span class="sxs-lookup"><span data-stu-id="546fd-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="546fd-520">Guid</span><span class="sxs-lookup"><span data-stu-id="546fd-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="546fd-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="546fd-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="546fd-522">Single</span><span class="sxs-lookup"><span data-stu-id="546fd-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="546fd-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="546fd-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="546fd-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="546fd-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="546fd-525">URI</span><span class="sxs-lookup"><span data-stu-id="546fd-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="546fd-526">Версия</span><span class="sxs-lookup"><span data-stu-id="546fd-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="546fd-527">Сложные типы</span><span class="sxs-lookup"><span data-stu-id="546fd-527">Complex types</span></span>

<span data-ttu-id="546fd-528">Сложный тип должен иметь открытый конструктор по умолчанию, а также открытые и доступные для записи свойства, подлежащие привязке.</span><span class="sxs-lookup"><span data-stu-id="546fd-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="546fd-529">Когда происходит привязка модели, класс создается с помощью открытого конструктора по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="546fd-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="546fd-530">Для каждого свойства сложного типа привязка модели ищет в источниках шаблон имени *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="546fd-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="546fd-531">Если ничего не найдено, он ищет только *property_name* без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="546fd-532">Для привязки к параметру префикс является именем параметра.</span><span class="sxs-lookup"><span data-stu-id="546fd-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="546fd-533">Для привязки к открытому свойству `PageModel` префикс является именем открытого свойства.</span><span class="sxs-lookup"><span data-stu-id="546fd-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="546fd-534">Некоторые атрибуты имеют свойство `Prefix`, которое позволяет переопределить использование по умолчанию для имени параметра или свойства.</span><span class="sxs-lookup"><span data-stu-id="546fd-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="546fd-535">Предположим, например, что сложный тип принадлежит к следующему классу `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="546fd-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="546fd-536">Префикс — это имя параметра</span><span class="sxs-lookup"><span data-stu-id="546fd-536">Prefix = parameter name</span></span>

<span data-ttu-id="546fd-537">Если модель, которую нужно привязать, является параметром с именем `instructorToUpdate`:</span><span class="sxs-lookup"><span data-stu-id="546fd-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="546fd-538">Привязка модели начинается с поиска источников ключа `instructorToUpdate.ID`.</span><span class="sxs-lookup"><span data-stu-id="546fd-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="546fd-539">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="546fd-540">Префикс — это имя свойства</span><span class="sxs-lookup"><span data-stu-id="546fd-540">Prefix = property name</span></span>

<span data-ttu-id="546fd-541">Если модель для привязки является свойством с именем `Instructor` контроллера или класса `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="546fd-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="546fd-542">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="546fd-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="546fd-543">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="546fd-544">Пользовательский префикс</span><span class="sxs-lookup"><span data-stu-id="546fd-544">Custom prefix</span></span>

<span data-ttu-id="546fd-545">Если модель для привязки — это параметр с именем `instructorToUpdate`, а атрибут `Bind` задает `Instructor` как префикс:</span><span class="sxs-lookup"><span data-stu-id="546fd-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="546fd-546">Привязка модели начинается с поиска источников ключа `Instructor.ID`.</span><span class="sxs-lookup"><span data-stu-id="546fd-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="546fd-547">Если он не найден, ищется `ID` без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="546fd-548">Атрибуты для целевых объектов сложного типа</span><span class="sxs-lookup"><span data-stu-id="546fd-548">Attributes for complex type targets</span></span>

<span data-ttu-id="546fd-549">Несколько встроенных атрибутов доступны для управления привязкой моделей сложных типов:</span><span class="sxs-lookup"><span data-stu-id="546fd-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="546fd-550">Эти атрибуты влияют на привязку модели, если опубликованные данные формы являются источником значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="546fd-551">Они не влияют на форматировщики входных данных, которые обрабатывают опубликованные тексты запросов JSON и XML.</span><span class="sxs-lookup"><span data-stu-id="546fd-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="546fd-552">Форматировщики входных данных описываются [далее в этой статье](#input-formatters).</span><span class="sxs-lookup"><span data-stu-id="546fd-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="546fd-553">Также см. обсуждение атрибута `[Required]` в разделе [Проверка модели](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="546fd-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="546fd-554">Атрибут [BindRequired]</span><span class="sxs-lookup"><span data-stu-id="546fd-554">[BindRequired] attribute</span></span>

<span data-ttu-id="546fd-555">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="546fd-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="546fd-556">Приводит к тому, что привязка модели добавляет ошибку состояния модели, если привязка для свойства модели невозможна.</span><span class="sxs-lookup"><span data-stu-id="546fd-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="546fd-557">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="546fd-558">Атрибут [BindNever]</span><span class="sxs-lookup"><span data-stu-id="546fd-558">[BindNever] attribute</span></span>

<span data-ttu-id="546fd-559">Может применяться только к свойствам модели, а не к параметрам метода.</span><span class="sxs-lookup"><span data-stu-id="546fd-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="546fd-560">Запрещает привязке модели задавать свойство модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="546fd-561">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="546fd-562">Атрибут [Bind]</span><span class="sxs-lookup"><span data-stu-id="546fd-562">[Bind] attribute</span></span>

<span data-ttu-id="546fd-563">Может быть применен к классу или параметру метода.</span><span class="sxs-lookup"><span data-stu-id="546fd-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="546fd-564">Указывает, какие свойства модели должны быть включены в привязку модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="546fd-565">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается любой метод действия или обработчик:</span><span class="sxs-lookup"><span data-stu-id="546fd-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="546fd-566">В следующем примере только указанные свойства модели `Instructor` привязываются, когда вызывается метод `OnPost`:</span><span class="sxs-lookup"><span data-stu-id="546fd-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="546fd-567">Атрибут `[Bind]` может использоваться для защиты от чрезмерной передачи данных при *создании*.</span><span class="sxs-lookup"><span data-stu-id="546fd-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="546fd-568">Он не работает в сценариях редактирования, поскольку исключенным свойствам задается значение NULL или значение по умолчанию, но не оставляется значение без изменений.</span><span class="sxs-lookup"><span data-stu-id="546fd-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="546fd-569">Для защиты от чрезмерной передачи данных рекомендуется использовать модели представлений вместо атрибута `[Bind]`.</span><span class="sxs-lookup"><span data-stu-id="546fd-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="546fd-570">Дополнительные сведения см. в разделе [Примечание по безопасности о чрезмерной передаче данных](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="546fd-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="546fd-571">Коллекции</span><span class="sxs-lookup"><span data-stu-id="546fd-571">Collections</span></span>

<span data-ttu-id="546fd-572">Для целевых объектов, которые являются коллекциями примитивных типов, привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="546fd-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="546fd-573">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="546fd-574">Пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-574">For example:</span></span>

* <span data-ttu-id="546fd-575">Предположим, что параметром для привязки является массив с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="546fd-576">Строковые данные формы или запроса могут иметь один из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="546fd-576">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="546fd-577">Следующий формат поддерживается только в данных формы:</span><span class="sxs-lookup"><span data-stu-id="546fd-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="546fd-578">Для всех перечисленных ранее форматов привязка модели передает массив из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="546fd-579">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="546fd-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="546fd-580">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="546fd-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="546fd-581">Форматы данных, которые используют номера нижних индексов (... [0] ... [1] ...), должны нумероваться последовательно, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="546fd-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="546fd-582">Если в нумерации есть пробелы, все элементы после пробела не учитываются.</span><span class="sxs-lookup"><span data-stu-id="546fd-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="546fd-583">Например, если указаны индексы 0 и 2, а не 0 и 1, второй элемент игнорируется.</span><span class="sxs-lookup"><span data-stu-id="546fd-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="546fd-584">Словари</span><span class="sxs-lookup"><span data-stu-id="546fd-584">Dictionaries</span></span>

<span data-ttu-id="546fd-585">Для целевых объектов `Dictionary` привязка модели ищет совпадения с *parameter_name* или *property_name*.</span><span class="sxs-lookup"><span data-stu-id="546fd-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="546fd-586">Если совпадений не найдено, она ищет один из поддерживаемых форматов без префикса.</span><span class="sxs-lookup"><span data-stu-id="546fd-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="546fd-587">Пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-587">For example:</span></span>

* <span data-ttu-id="546fd-588">Предположим, что целевой параметр является `Dictionary<int, string>` с именем `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="546fd-589">Опубликованные строковые данные формы или запроса могут выглядеть как один из следующих примеров:</span><span class="sxs-lookup"><span data-stu-id="546fd-589">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="546fd-590">Для всех перечисленных ранее форматов привязка модели передает словарь из двух элементов в параметр `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="546fd-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="546fd-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="546fd-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="546fd-592">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="546fd-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="546fd-593">Поведение глобализации для данных маршрутов привязки модели и строк запросов</span><span class="sxs-lookup"><span data-stu-id="546fd-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="546fd-594">Поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="546fd-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="546fd-595">обрабатывают значения как имеющие инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="546fd-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="546fd-596">Следует ожидать, что URL-адреса имеют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="546fd-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="546fd-597">Напротив, значения, поступающие из данных форм, подвергаются преобразованию с учетом языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="546fd-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="546fd-598">Это сделано намеренно, чтобы URL-адреса были общими в разных языковых стандартах.</span><span class="sxs-lookup"><span data-stu-id="546fd-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="546fd-599">Чтобы поставщик значений маршрутов и поставщик значений для строк запросов ASP.NET Core производили преобразование с учетом языка и региональных параметров, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="546fd-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="546fd-600">наследуют от <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="546fd-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="546fd-601">Скопируйте код из [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) или [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span><span class="sxs-lookup"><span data-stu-id="546fd-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="546fd-602">Замените [значение языка и региональных параметров](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30), передаваемое в конструктор поставщика значений, на [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span><span class="sxs-lookup"><span data-stu-id="546fd-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="546fd-603">Замените метод производства поставщика значений по умолчанию в параметрах MVC на новый:</span><span class="sxs-lookup"><span data-stu-id="546fd-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="546fd-604">Специальные типы данных</span><span class="sxs-lookup"><span data-stu-id="546fd-604">Special data types</span></span>

<span data-ttu-id="546fd-605">Существуют некоторые особые типы данных, которые может обрабатывать привязка модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="546fd-606">IFormFile и IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="546fd-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="546fd-607">Переданный файл, включенный в HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="546fd-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="546fd-608">Также поддерживается `IEnumerable<IFormFile>` для нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="546fd-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="546fd-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="546fd-609">CancellationToken</span></span>

<span data-ttu-id="546fd-610">используется для отмены действия в асинхронных контроллерах.</span><span class="sxs-lookup"><span data-stu-id="546fd-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="546fd-611">FormCollection</span><span class="sxs-lookup"><span data-stu-id="546fd-611">FormCollection</span></span>

<span data-ttu-id="546fd-612">Используется для извлечения всех значений из опубликованных данных формы.</span><span class="sxs-lookup"><span data-stu-id="546fd-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="546fd-613">Форматировщики входных данных</span><span class="sxs-lookup"><span data-stu-id="546fd-613">Input formatters</span></span>

<span data-ttu-id="546fd-614">Данные в тексте запроса могут быть в формате JSON, XML или другом формате.</span><span class="sxs-lookup"><span data-stu-id="546fd-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="546fd-615">Для анализа этих данных модель привязки использует *форматировщик входных данных*, настроенный для обработки определенного типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="546fd-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="546fd-616">По умолчанию ASP.NET Core включает форматировщики входных данных на основе JSON для обработки данных JSON.</span><span class="sxs-lookup"><span data-stu-id="546fd-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="546fd-617">Вы можете добавить другие форматировщики для других типов содержимого.</span><span class="sxs-lookup"><span data-stu-id="546fd-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="546fd-618">ASP.NET Core выбирает форматировщики входных данных на основе атрибута [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute).</span><span class="sxs-lookup"><span data-stu-id="546fd-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="546fd-619">Если атрибут отсутствует, используется [Заголовок Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="546fd-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="546fd-620">Чтобы использовать встроенные форматировщики входных данных XML:</span><span class="sxs-lookup"><span data-stu-id="546fd-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="546fd-621">Установите пакет NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="546fd-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="546fd-622">В `Startup.ConfigureServices` вызовите <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> или <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span><span class="sxs-lookup"><span data-stu-id="546fd-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="546fd-623">Примените атрибут `Consumes` к классам контроллера или методам действий, которые должны ожидать XML в тексте запроса.</span><span class="sxs-lookup"><span data-stu-id="546fd-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="546fd-624">Дополнительные сведения см. в разделе [Введение в сериализацию XML](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="546fd-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="546fd-625">Исключение указанных типов из привязки модели</span><span class="sxs-lookup"><span data-stu-id="546fd-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="546fd-626">Поведение привязки модели и системы проверки определяется классом [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span><span class="sxs-lookup"><span data-stu-id="546fd-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="546fd-627">Вы можете настроить `ModelMetadata`, добавив поставщик сведений в [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span><span class="sxs-lookup"><span data-stu-id="546fd-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="546fd-628">Встроенные поставщики сведений доступны для отключения привязки модели или проверки для указанных типов.</span><span class="sxs-lookup"><span data-stu-id="546fd-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="546fd-629">Чтобы отключить привязку модели для всех моделей указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="546fd-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="546fd-630">Например, для отключения привязки модели для всех моделей типа `System.Version`:</span><span class="sxs-lookup"><span data-stu-id="546fd-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="546fd-631">Чтобы отключить проверку свойств указанного типа, добавьте <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="546fd-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="546fd-632">Например, чтобы отключить проверку по свойствам типа `System.Guid`:</span><span class="sxs-lookup"><span data-stu-id="546fd-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="546fd-633">Настраиваемые связыватели модели</span><span class="sxs-lookup"><span data-stu-id="546fd-633">Custom model binders</span></span>

<span data-ttu-id="546fd-634">Привязку модели можно расширить путем написания пользовательского связывателя модели и с помощью атрибута `[ModelBinder]`, чтобы выбрать его для заданного целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="546fd-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="546fd-635">Узнайте больше о [пользовательской привязке модели](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="546fd-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="546fd-636">Привязка модели вручную</span><span class="sxs-lookup"><span data-stu-id="546fd-636">Manual model binding</span></span>

<span data-ttu-id="546fd-637">Привязка модели может вызываться вручную с помощью метода <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>.</span><span class="sxs-lookup"><span data-stu-id="546fd-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="546fd-638">Этот метод определен в классах `ControllerBase` и `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="546fd-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="546fd-639">Перегрузки метода позволяют задать поставщик префиксов и значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="546fd-640">Этот метод возвращает `false` при сбое привязки модели.</span><span class="sxs-lookup"><span data-stu-id="546fd-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="546fd-641">Ниже приведен пример:</span><span class="sxs-lookup"><span data-stu-id="546fd-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="546fd-642">Атрибут [FromServices]</span><span class="sxs-lookup"><span data-stu-id="546fd-642">[FromServices] attribute</span></span>

<span data-ttu-id="546fd-643">Имя этого атрибута соответствует шаблону атрибутов привязки модели, которые указывают источник данных.</span><span class="sxs-lookup"><span data-stu-id="546fd-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="546fd-644">Но это не связано с привязкой данных от поставщика значений.</span><span class="sxs-lookup"><span data-stu-id="546fd-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="546fd-645">Он получает экземпляр типа из контейнера [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="546fd-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="546fd-646">Он предназначен для предоставления альтернативы внедрению через конструктор, когда вам нужна служба, только если вызывается конкретный метод.</span><span class="sxs-lookup"><span data-stu-id="546fd-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="546fd-647">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="546fd-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
