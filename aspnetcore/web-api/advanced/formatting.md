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
ms.openlocfilehash: e3e176072032494f94f120efabb95d6c127fb5e6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587818"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="94fc2-103">Форматирование данных отклика в веб-API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94fc2-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="94fc2-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="94fc2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="94fc2-105">Приложение MVC ASP.NET Core поддерживает форматирование данных ответа.</span><span class="sxs-lookup"><span data-stu-id="94fc2-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="94fc2-106">Данные ответа могут возвращаться в определенных форматах или в формате, запрошенном клиентом.</span><span class="sxs-lookup"><span data-stu-id="94fc2-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="94fc2-107">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/formatting) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="94fc2-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="94fc2-108">Результаты действий для конкретного формата</span><span class="sxs-lookup"><span data-stu-id="94fc2-108">Format-specific Action Results</span></span>

<span data-ttu-id="94fc2-109">Некоторые типы результатов действий характерны для определенного формата, например <xref:Microsoft.AspNetCore.Mvc.JsonResult> и <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="94fc2-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="94fc2-110">Действия могут возвращать результаты в определенном формате независимо от настроек клиента.</span><span class="sxs-lookup"><span data-stu-id="94fc2-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="94fc2-111">Например, при возврате `JsonResult` возвращаются данные в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="94fc2-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="94fc2-112">При возврате `ContentResult` или строки возвращаются строковые данные в формате обычного текста.</span><span class="sxs-lookup"><span data-stu-id="94fc2-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="94fc2-113">Действие не должно возвращать данные конкретного типа.</span><span class="sxs-lookup"><span data-stu-id="94fc2-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="94fc2-114">ASP.NET Core поддерживает любое возвращаемое значение объекта.</span><span class="sxs-lookup"><span data-stu-id="94fc2-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="94fc2-115">Результаты из действий, возвращающих объекты, которые не являются типами <xref:Microsoft.AspNetCore.Mvc.IActionResult>, сериализуются с помощью соответствующей реализации <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="94fc2-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="94fc2-116">Для получения дополнительной информации см. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="94fc2-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="94fc2-117">Встроенный вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> возвращает данные в формате JSON: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="94fc2-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="94fc2-118">Скачанный пример возвращает список авторов.</span><span class="sxs-lookup"><span data-stu-id="94fc2-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="94fc2-119">При использовании средств разработчика в браузере (F12) или [Postman](https://www.getpostman.com/tools) с предыдущим кодом:</span><span class="sxs-lookup"><span data-stu-id="94fc2-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="94fc2-120">Отобразится заголовок ответа, содержащий **content-type:** `application/json; charset=utf-8`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="94fc2-121">Отобразятся заголовки запросов.</span><span class="sxs-lookup"><span data-stu-id="94fc2-121">The request headers are displayed.</span></span> <span data-ttu-id="94fc2-122">Например, заголовок `Accept`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-122">For example, the `Accept` header.</span></span> <span data-ttu-id="94fc2-123">Приведенный выше код игнорирует заголовок `Accept`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="94fc2-124">Чтобы возвратить данные в формате обычного текста, используйте <xref:Microsoft.AspNetCore.Mvc.ContentResult> и вспомогательный метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>:</span><span class="sxs-lookup"><span data-stu-id="94fc2-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="94fc2-125">В приведенном выше коде возвращаемым `Content-Type` является `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="94fc2-126">При возврате строки возвращается `Content-Type` со значением `text/plain`:</span><span class="sxs-lookup"><span data-stu-id="94fc2-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="94fc2-127">Для действий с несколькими возвращаемыми типами возвращается значение `IActionResult`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="94fc2-128">Например, возвращаются различные коды состояния HTTP на основе результатов выполненных операций.</span><span class="sxs-lookup"><span data-stu-id="94fc2-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="94fc2-129">Согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="94fc2-129">Content negotiation</span></span>

<span data-ttu-id="94fc2-130">Согласование содержимого происходит, когда клиент задает [заголовок Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="94fc2-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="94fc2-131">Для ASP.NET Core по умолчанию используется формат [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="94fc2-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="94fc2-132">Согласование содержимого:</span><span class="sxs-lookup"><span data-stu-id="94fc2-132">Content negotiation is:</span></span>

* <span data-ttu-id="94fc2-133">реализуется с помощью <xref:Microsoft.AspNetCore.Mvc.ObjectResult>;</span><span class="sxs-lookup"><span data-stu-id="94fc2-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="94fc2-134">встроено в результаты действия с определенным кодом состояния, возвращаемые из вспомогательных методов;</span><span class="sxs-lookup"><span data-stu-id="94fc2-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="94fc2-135">вспомогательные методы результатов действия основаны на `ObjectResult`;</span><span class="sxs-lookup"><span data-stu-id="94fc2-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="94fc2-136">при возврате типа модели возвращаемым типом является `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="94fc2-137">Следующий метод действия использует вспомогательные методы `Ok` и `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="94fc2-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="94fc2-138">По умолчанию ASP.NET Core поддерживает типы мультимедиа `application/json`, `text/json` и `text/plain`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="94fc2-139">Средства, такие как [Fiddler](https://www.telerik.com/fiddler) или [Postman](https://www.getpostman.com/tools), могут установить заголовок запроса `Accept`, чтобы указать формат возвращаемого значения.</span><span class="sxs-lookup"><span data-stu-id="94fc2-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="94fc2-140">Если заголовок `Accept` содержит тип, который поддерживается сервером, возвращается этот тип.</span><span class="sxs-lookup"><span data-stu-id="94fc2-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="94fc2-141">Сведения о добавлении дополнительных форматировщиков приведены в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="94fc2-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="94fc2-142">Действия контроллера могут возвращать POCO (простые объекты CLR).</span><span class="sxs-lookup"><span data-stu-id="94fc2-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="94fc2-143">При возвращении POCO среда выполнения автоматически создает `ObjectResult`, который генерирует оболочку объекта.</span><span class="sxs-lookup"><span data-stu-id="94fc2-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="94fc2-144">Клиент получает отформатированный сериализованный объект.</span><span class="sxs-lookup"><span data-stu-id="94fc2-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="94fc2-145">Если возвращаемый объект имеет значение `null`, возвращается ответ `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="94fc2-146">Возвращение типа объекта:</span><span class="sxs-lookup"><span data-stu-id="94fc2-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="94fc2-147">В приведенном выше коде запрос допустимого псевдонима автора получает ответ `200 OK` с данными об авторе.</span><span class="sxs-lookup"><span data-stu-id="94fc2-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="94fc2-148">Запрос недопустимого псевдонима возвращает ответ `204 No Content`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="94fc2-149">Заголовок Accept</span><span class="sxs-lookup"><span data-stu-id="94fc2-149">The Accept header</span></span>

<span data-ttu-id="94fc2-150">*Согласование* содержимого выполняется только при наличии в запросе заголовка `Accept`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="94fc2-151">Если запрос содержит заголовок Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="94fc2-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="94fc2-152">перечисляет типы мультимедиа в заголовке Accept в порядке предпочтения;</span><span class="sxs-lookup"><span data-stu-id="94fc2-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="94fc2-153">пытается найти форматировщик, который может создать ответ в одном из указанных форматов.</span><span class="sxs-lookup"><span data-stu-id="94fc2-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="94fc2-154">Если форматировщик, который может удовлетворить запрос клиента, не найден, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="94fc2-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="94fc2-155">Возвращает `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> , если имеет значение `true` , или-</span><span class="sxs-lookup"><span data-stu-id="94fc2-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="94fc2-156">пытается найти первый форматировщик, который может создать ответ.</span><span class="sxs-lookup"><span data-stu-id="94fc2-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="94fc2-157">Если форматировщик, обеспечивающий требуемый формат, не настроен, используется первый форматировщик, способный отформатировать данный объект.</span><span class="sxs-lookup"><span data-stu-id="94fc2-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="94fc2-158">Если в запросе не отображается заголовок `Accept`:</span><span class="sxs-lookup"><span data-stu-id="94fc2-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="94fc2-159">Для сериализации ответа используется первый форматировщик, который может работать с объектом.</span><span class="sxs-lookup"><span data-stu-id="94fc2-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="94fc2-160">Согласование не выполняется.</span><span class="sxs-lookup"><span data-stu-id="94fc2-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="94fc2-161">Сервер определяет возвращаемый формат.</span><span class="sxs-lookup"><span data-stu-id="94fc2-161">The server is determining what format to return.</span></span>

<span data-ttu-id="94fc2-162">Если заголовок Accept содержит `*/*`, он игнорируется, если только `RespectBrowserAcceptHeader` не имеет значение true в <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span><span class="sxs-lookup"><span data-stu-id="94fc2-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="94fc2-163">Браузеры и согласование содержимого</span><span class="sxs-lookup"><span data-stu-id="94fc2-163">Browsers and content negotiation</span></span>

<span data-ttu-id="94fc2-164">В отличие от обычных клиентов API, веб-браузеры предоставляют заголовки `Accept`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="94fc2-165">Веб-браузер определяет множество форматов, включая подстановочные знаки.</span><span class="sxs-lookup"><span data-stu-id="94fc2-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="94fc2-166">Если платформа обнаруживает, что запрос поступает из браузера, по умолчанию выполняется следующее:</span><span class="sxs-lookup"><span data-stu-id="94fc2-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="94fc2-167">Заголовок `Accept` игнорируется.</span><span class="sxs-lookup"><span data-stu-id="94fc2-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="94fc2-168">Содержимое возвращается в формате JSON, если не указано иначе.</span><span class="sxs-lookup"><span data-stu-id="94fc2-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="94fc2-169">Это обеспечивает более согласованное взаимодействие между браузерами при использовании API.</span><span class="sxs-lookup"><span data-stu-id="94fc2-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="94fc2-170">Чтобы настроить приложение для учета заголовков Accept в браузере, установите для параметра <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> значение `true`:</span><span class="sxs-lookup"><span data-stu-id="94fc2-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="94fc2-171">Настройка форматировщиков</span><span class="sxs-lookup"><span data-stu-id="94fc2-171">Configure formatters</span></span>

<span data-ttu-id="94fc2-172">В приложениях, которым требуется поддержка дополнительных форматов, можно добавлять соответствующие пакеты NuGet и настраивать поддержку.</span><span class="sxs-lookup"><span data-stu-id="94fc2-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="94fc2-173">Существуют отдельные модули форматирования для ввода и вывода.</span><span class="sxs-lookup"><span data-stu-id="94fc2-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="94fc2-174">Форматировщики ввода используются [привязкой модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="94fc2-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="94fc2-175">Форматировщики вывода используются для форматирования ответов.</span><span class="sxs-lookup"><span data-stu-id="94fc2-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="94fc2-176">Сведения о создании пользовательского форматировщика см. в статье [Пользовательские модули форматирования для веб-API в ASP.NET Core](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="94fc2-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="94fc2-177">Добавление поддержки формата XML</span><span class="sxs-lookup"><span data-stu-id="94fc2-177">Add XML format support</span></span>

<span data-ttu-id="94fc2-178">Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="94fc2-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="94fc2-179">Приведенный выше код сериализует результаты с помощью `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="94fc2-180">При использовании приведенного выше кода методы контроллера возвращают соответствующий формат на основе заголовка `Accept` запроса.</span><span class="sxs-lookup"><span data-stu-id="94fc2-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="94fc2-181">Настройка `System.Text.Json` модулей форматирования на основе</span><span class="sxs-lookup"><span data-stu-id="94fc2-181">Configure `System.Text.Json` based formatters</span></span>

<span data-ttu-id="94fc2-182">Функции для `System.Text.Json` модулей форматирования на основе можно настроить с помощью <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="94fc2-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="94fc2-183">Форматирование по умолчанию — camelCase.</span><span class="sxs-lookup"><span data-stu-id="94fc2-183">The default formatting is camelCase.</span></span> <span data-ttu-id="94fc2-184">Следующий выделенный код задает форматирование PascalCase:</span><span class="sxs-lookup"><span data-stu-id="94fc2-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="94fc2-185">Следующий метод действия вызывает [ControllerBase. проблема](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) , чтобы создать <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> Ответ:</span><span class="sxs-lookup"><span data-stu-id="94fc2-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="94fc2-186">С приведенным выше кодом:</span><span class="sxs-lookup"><span data-stu-id="94fc2-186">With the preceding code:</span></span>

  * <span data-ttu-id="94fc2-187">`https://localhost:5001/WeatherForecast/temperature` Возвращает PascalCase.</span><span class="sxs-lookup"><span data-stu-id="94fc2-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="94fc2-188">`https://localhost:5001/WeatherForecast/error` Возвращает camelCase.</span><span class="sxs-lookup"><span data-stu-id="94fc2-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="94fc2-189">Ответ об ошибке всегда camelCase, даже если приложение устанавливает формат PascalCase.</span><span class="sxs-lookup"><span data-stu-id="94fc2-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="94fc2-190">`ProblemDetails` соответствует стандарту [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), который указывает нижний регистр</span><span class="sxs-lookup"><span data-stu-id="94fc2-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="94fc2-191">Следующий код задает PascalCase и добавляет пользовательский преобразователь:</span><span class="sxs-lookup"><span data-stu-id="94fc2-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="94fc2-192">Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="94fc2-193">Пример:</span><span class="sxs-lookup"><span data-stu-id="94fc2-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="94fc2-194">Добавление поддержки формата JSON на основе Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="94fc2-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="94fc2-195">До выпуска версии ASP.NET Core 3.0 по умолчанию использовались форматировщики JSON, реализованные с помощью пакета `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="94fc2-196">В ASP.NET Core 3.0 или более поздней версии форматировщики JSON по умолчанию основаны на `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="94fc2-197">Поддержка `Newtonsoft.Json` модулей форматирования и функций на основе данных доступна путем установки [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) пакета NuGet и его настройки в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="94fc2-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="94fc2-198">В приведенном выше коде вызов `AddNewtonsoftJson` настраивает следующие функции веб-API, MVC и Razor страниц для использования `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="94fc2-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="94fc2-199">Модули форматирования ввода и вывода, считывающие и записывающие JSON</span><span class="sxs-lookup"><span data-stu-id="94fc2-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="94fc2-200">JSON PATCH</span><span class="sxs-lookup"><span data-stu-id="94fc2-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="94fc2-201">TempData</span><span class="sxs-lookup"><span data-stu-id="94fc2-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="94fc2-202">Возможно, некоторые функции не будут оптимально работать с форматировщиками на основе `System.Text.Json` и будут требовать ссылки на форматировщики на основе `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="94fc2-203">Продолжайте использовать форматировщики на основе `Newtonsoft.Json`, если приложение:</span><span class="sxs-lookup"><span data-stu-id="94fc2-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="94fc2-204">Использует атрибут `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="94fc2-205">Например, `[JsonProperty]` или `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="94fc2-206">Настраивает параметры сериализации.</span><span class="sxs-lookup"><span data-stu-id="94fc2-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="94fc2-207">Зависит от функций, предоставляемых `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="94fc2-208">Настраивается `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="94fc2-209">В ASP.NET Core более ранних версий, чем версия 3.0, `JsonResult.SerializerSettings` принимает экземпляр `JsonSerializerSettings`, который относится к `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="94fc2-210">Создается документация [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="94fc2-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="94fc2-211">Функции для форматировщиков на основе `Newtonsoft.Json` можно настроить с помощью `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span><span class="sxs-lookup"><span data-stu-id="94fc2-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="94fc2-212">Параметры сериализации выходных данных для отдельных действий можно настроить с помощью `JsonResult`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="94fc2-213">Пример:</span><span class="sxs-lookup"><span data-stu-id="94fc2-213">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="94fc2-214">Добавление поддержки формата XML</span><span class="sxs-lookup"><span data-stu-id="94fc2-214">Add XML format support</span></span>

<span data-ttu-id="94fc2-215">Для форматирования XML требуется пакет NuGet [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).</span><span class="sxs-lookup"><span data-stu-id="94fc2-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="94fc2-216">Форматировщики XML, реализованные с помощью <xref:System.Xml.Serialization.XmlSerializer>, можно настроить путем вызова <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span><span class="sxs-lookup"><span data-stu-id="94fc2-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="94fc2-217">Приведенный выше код сериализует результаты с помощью `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="94fc2-218">При использовании приведенного выше кода методы контроллера должны возвращать соответствующий формат на основе заголовка `Accept` запроса.</span><span class="sxs-lookup"><span data-stu-id="94fc2-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="94fc2-219">Указание формата</span><span class="sxs-lookup"><span data-stu-id="94fc2-219">Specify a format</span></span>

<span data-ttu-id="94fc2-220">Чтобы ограничить форматы ответа, примените [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) фильтр.</span><span class="sxs-lookup"><span data-stu-id="94fc2-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="94fc2-221">Как и большинство [фильтров](xref:mvc/controllers/filters), `[Produces]` можно применить к действию, контроллеру или глобальной области:</span><span class="sxs-lookup"><span data-stu-id="94fc2-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="94fc2-222">Предыдущий [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Фильтр:</span><span class="sxs-lookup"><span data-stu-id="94fc2-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="94fc2-223">Заставляет все действия в контроллере возвращать ответы в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="94fc2-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="94fc2-224">Если другие форматировщики настроены и клиент указывает другой формат, возвращается JSON.</span><span class="sxs-lookup"><span data-stu-id="94fc2-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="94fc2-225">Дополнительные сведения см. в статье [Фильтры в ASP.NET Core](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="94fc2-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="94fc2-226">Специальные форматировщики</span><span class="sxs-lookup"><span data-stu-id="94fc2-226">Special case formatters</span></span>

<span data-ttu-id="94fc2-227">Встроенные модули форматирования реализуют некоторые специальные возможности.</span><span class="sxs-lookup"><span data-stu-id="94fc2-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="94fc2-228">По умолчанию типы возвращаемых значений `string` форматируются как *text/plain* (*text/html*, если того требует заголовок `Accept`).</span><span class="sxs-lookup"><span data-stu-id="94fc2-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="94fc2-229">Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="94fc2-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="94fc2-230">Форматировщики удаляются в методе `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="94fc2-231">Действия, у которых типом возвращаемого объекта является модель, возвращают ответ `204 No Content` при возврате значения `null`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="94fc2-232">Это поведение можно отключить, удалив <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span><span class="sxs-lookup"><span data-stu-id="94fc2-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="94fc2-233">Приведенный ниже код удаляет `StringOutputFormatter` и `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="94fc2-234">Без `StringOutputFormatter`, встроенный модуль форматирования JSON форматирует типы возвращаемого значения `string`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="94fc2-235">Если встроенный модуль форматирования JSON удален и доступен модуль форматирования XML, то типы возвращаемого значения `string` форматирует модуль форматирования XML.</span><span class="sxs-lookup"><span data-stu-id="94fc2-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="94fc2-236">В противном случае, `string` типы возвращаемого значения возвращают `406 Not Acceptable`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="94fc2-237">Без `HttpNoContentOutputFormatter` объекты со значением null форматируются с помощью настроенного модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="94fc2-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="94fc2-238">Пример:</span><span class="sxs-lookup"><span data-stu-id="94fc2-238">For example:</span></span>

* <span data-ttu-id="94fc2-239">Форматировщик JSON возвращает ответ с текстом `null`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="94fc2-240">Форматировщик XML возвращает пустой XML-элемент с атрибутом `xsi:nil="true"`.</span><span class="sxs-lookup"><span data-stu-id="94fc2-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="94fc2-241">Сопоставления URL-адреса для формата ответа</span><span class="sxs-lookup"><span data-stu-id="94fc2-241">Response format URL mappings</span></span>

<span data-ttu-id="94fc2-242">Клиенты могут запрашивать определенный формат как часть URL-адреса, например:</span><span class="sxs-lookup"><span data-stu-id="94fc2-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="94fc2-243">В строке запроса или в части пути.</span><span class="sxs-lookup"><span data-stu-id="94fc2-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="94fc2-244">С использованием расширения файла конкретного формата, такого как XML или JSON.</span><span class="sxs-lookup"><span data-stu-id="94fc2-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="94fc2-245">Сопоставление из пути запроса должно быть указано в маршруте, используемом API.</span><span class="sxs-lookup"><span data-stu-id="94fc2-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="94fc2-246">Пример:</span><span class="sxs-lookup"><span data-stu-id="94fc2-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="94fc2-247">Этот маршрут позволяет задать запрошенный формат в качестве дополнительного расширения файла.</span><span class="sxs-lookup"><span data-stu-id="94fc2-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="94fc2-248">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Атрибут проверяет наличие значения формата в `RouteData` и сопоставляет формат ответа с соответствующим модулем форматирования при создании ответа.</span><span class="sxs-lookup"><span data-stu-id="94fc2-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="94fc2-249">Маршрут</span><span class="sxs-lookup"><span data-stu-id="94fc2-249">Route</span></span>        |             <span data-ttu-id="94fc2-250">Formatter</span><span class="sxs-lookup"><span data-stu-id="94fc2-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="94fc2-251">Модуль форматирования вывода по умолчанию</span><span class="sxs-lookup"><span data-stu-id="94fc2-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="94fc2-252">Модуль форматирования JSON (если настроен)</span><span class="sxs-lookup"><span data-stu-id="94fc2-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="94fc2-253">Модуль форматирования XML (если настроен)</span><span class="sxs-lookup"><span data-stu-id="94fc2-253">The XML formatter (if configured)</span></span>  |
