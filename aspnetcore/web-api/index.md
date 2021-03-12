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
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="3f36e-103">Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f36e-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="3f36e-104">Авторы: [Скотт Адди](https://github.com/scottaddie) (Scott Addie) и [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra)</span><span class="sxs-lookup"><span data-stu-id="3f36e-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3f36e-105">ASP.NET Core поддерживает создание служб RESTful, также известных как веб-API, с помощью C#.</span><span class="sxs-lookup"><span data-stu-id="3f36e-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="3f36e-106">Для обработки запросов веб-API использует контроллеры.</span><span class="sxs-lookup"><span data-stu-id="3f36e-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="3f36e-107">В веб-API *контроллеры* — это классы, производные от `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="3f36e-108">В этой статье показано, как использовать контроллеры для обработки веб-запросов API.</span><span class="sxs-lookup"><span data-stu-id="3f36e-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="3f36e-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="3f36e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="3f36e-110">([Инструкция по скачиванию](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="3f36e-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="3f36e-111">Класс ControllerBase</span><span class="sxs-lookup"><span data-stu-id="3f36e-111">ControllerBase class</span></span>

<span data-ttu-id="3f36e-112">Веб-API состоит из одного или нескольких классов контроллера, которые являются производными от <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="3f36e-113">Шаблон проекта веб-API предоставляет начальный контроллер:</span><span class="sxs-lookup"><span data-stu-id="3f36e-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="3f36e-114">Не создавайте контроллер веб-API путем наследования от класса <xref:Microsoft.AspNetCore.Mvc.Controller>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="3f36e-115">`Controller` является производным от `ControllerBase` и добавляет поддержку для представлений, обеспечивая обработку веб-страниц, а не запросов веб-API.</span><span class="sxs-lookup"><span data-stu-id="3f36e-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="3f36e-116">Существует одно исключение из этого правила: если вы планируете использовать один и тот же контроллер для представлений и веб-API, сделайте его производным от `Controller`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="3f36e-117">Класс `ControllerBase` предоставляет множество свойств и методов, которые удобны для обработки HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="3f36e-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="3f36e-118">Например, `ControllerBase.CreatedAtAction` возвращает код состояния 201.</span><span class="sxs-lookup"><span data-stu-id="3f36e-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="3f36e-119">Вот еще примеры методов, предоставляющих `ControllerBase`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="3f36e-120">Метод</span><span class="sxs-lookup"><span data-stu-id="3f36e-120">Method</span></span>   |<span data-ttu-id="3f36e-121">Примечания</span><span class="sxs-lookup"><span data-stu-id="3f36e-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="3f36e-122">Возвращает код состояния 400.</span><span class="sxs-lookup"><span data-stu-id="3f36e-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="3f36e-123">Возвращает код состояния 404.</span><span class="sxs-lookup"><span data-stu-id="3f36e-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="3f36e-124">Возвращает файл.</span><span class="sxs-lookup"><span data-stu-id="3f36e-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="3f36e-125">Вызывает [привязку модели](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="3f36e-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="3f36e-126">Вызывает [проверку модели](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3f36e-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="3f36e-127">Список всех доступных методов и свойств см. здесь: <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="3f36e-128">Атрибуты</span><span class="sxs-lookup"><span data-stu-id="3f36e-128">Attributes</span></span>

<span data-ttu-id="3f36e-129">Пространство имен <xref:Microsoft.AspNetCore.Mvc> предоставляет атрибуты, которые можно использовать для настройки поведения контроллеров и методов действия веб-API.</span><span class="sxs-lookup"><span data-stu-id="3f36e-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="3f36e-130">В следующем примере атрибуты используются для указания поддерживаемой команды действия HTTP и всех известных кодов состояния HTTP, которые могут быть возвращены:</span><span class="sxs-lookup"><span data-stu-id="3f36e-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="3f36e-131">Вот еще примеры доступных атрибутов.</span><span class="sxs-lookup"><span data-stu-id="3f36e-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="3f36e-132">Атрибут</span><span class="sxs-lookup"><span data-stu-id="3f36e-132">Attribute</span></span>|<span data-ttu-id="3f36e-133">Примечания</span><span class="sxs-lookup"><span data-stu-id="3f36e-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="3f36e-134">Определяет шаблон URL-адреса для контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="3f36e-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="3f36e-135">Задает префикс и свойства, которые добавляются для привязки модели.</span><span class="sxs-lookup"><span data-stu-id="3f36e-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="3f36e-136">Определяет действие, которое поддерживает команду действия HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3f36e-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="3f36e-137">Указывает типы данных, которые принимает действие.</span><span class="sxs-lookup"><span data-stu-id="3f36e-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="3f36e-138">Указывает типы данных, которые возвращает действие.</span><span class="sxs-lookup"><span data-stu-id="3f36e-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="3f36e-139">Список доступных атрибутов см. в пространстве имен <xref:Microsoft.AspNetCore.Mvc>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="3f36e-140">Атрибут ApiController</span><span class="sxs-lookup"><span data-stu-id="3f36e-140">ApiController attribute</span></span>

<span data-ttu-id="3f36e-141">Атрибут [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) можно применить к классу контроллера для включения следующих специализированных схем поведения API:</span><span class="sxs-lookup"><span data-stu-id="3f36e-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="3f36e-142">Обязательная маршрутизация атрибутов</span><span class="sxs-lookup"><span data-stu-id="3f36e-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="3f36e-143">Автоматические отклики HTTP 400</span><span class="sxs-lookup"><span data-stu-id="3f36e-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="3f36e-144">Вывод параметров источника привязки</span><span class="sxs-lookup"><span data-stu-id="3f36e-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="3f36e-145">Вывод многокомпонентных запросов и запросов данных форм</span><span class="sxs-lookup"><span data-stu-id="3f36e-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="3f36e-146">Сведения о проблемах для кодов состояния ошибки</span><span class="sxs-lookup"><span data-stu-id="3f36e-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="3f36e-147">Для использования функции *Сведения о проблеме для кодов состояния ошибки* требуется [совместимая версия](xref:mvc/compatibility-version) 2.2 и выше.</span><span class="sxs-lookup"><span data-stu-id="3f36e-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="3f36e-148">Для реализации других функций требуется совместимая версия 2.1 и выше.</span><span class="sxs-lookup"><span data-stu-id="3f36e-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

* [<span data-ttu-id="3f36e-149">Обязательная маршрутизация атрибутов</span><span class="sxs-lookup"><span data-stu-id="3f36e-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="3f36e-150">Автоматические отклики HTTP 400</span><span class="sxs-lookup"><span data-stu-id="3f36e-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="3f36e-151">Вывод параметров источника привязки</span><span class="sxs-lookup"><span data-stu-id="3f36e-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="3f36e-152">Вывод многокомпонентных запросов и запросов данных форм</span><span class="sxs-lookup"><span data-stu-id="3f36e-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="3f36e-153">Для реализации этих функций необходима [версия совместимости](xref:mvc/compatibility-version), начиная с 2.1.</span><span class="sxs-lookup"><span data-stu-id="3f36e-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="3f36e-154">Атрибут в определенных контроллерах</span><span class="sxs-lookup"><span data-stu-id="3f36e-154">Attribute on specific controllers</span></span>

<span data-ttu-id="3f36e-155">Атрибут `[ApiController]` может применяться к определенным контроллерам, как показано в следующем примере из шаблона проекта:</span><span class="sxs-lookup"><span data-stu-id="3f36e-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="3f36e-156">Атрибут в нескольких контроллерах</span><span class="sxs-lookup"><span data-stu-id="3f36e-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="3f36e-157">Один из подходов к использованию атрибута на более чем одном контроллере заключается в создании пользовательского базового класса контроллера, аннотированного атрибутом `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="3f36e-158">В следующем примере демонстрируется пользовательский базовый класс и производный от него контроллер:</span><span class="sxs-lookup"><span data-stu-id="3f36e-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="3f36e-159">Атрибут в сборке</span><span class="sxs-lookup"><span data-stu-id="3f36e-159">Attribute on an assembly</span></span>

<span data-ttu-id="3f36e-160">Если задана [версия совместимости](xref:mvc/compatibility-version) 2.2 или последующая, атрибут `[ApiController]` можно применить к сборке.</span><span class="sxs-lookup"><span data-stu-id="3f36e-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="3f36e-161">Аннотирование этим способом применяет поведение веб-API ко всем контроллерам в сборке.</span><span class="sxs-lookup"><span data-stu-id="3f36e-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="3f36e-162">Его нельзя отменить для отдельных контроллеров.</span><span class="sxs-lookup"><span data-stu-id="3f36e-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="3f36e-163">Примените атрибут уровня сборки к объявлению пространства имен, окружающему класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="3f36e-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="3f36e-164">Обязательная маршрутизация атрибутов</span><span class="sxs-lookup"><span data-stu-id="3f36e-164">Attribute routing requirement</span></span>

<span data-ttu-id="3f36e-165">Атрибут `[ApiController]` требует обязательной маршрутизации атрибутов.</span><span class="sxs-lookup"><span data-stu-id="3f36e-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="3f36e-166">Пример:</span><span class="sxs-lookup"><span data-stu-id="3f36e-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="3f36e-167">Действия недоступны через [обычные маршруты](xref:mvc/controllers/routing#conventional-routing), определяемые `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> или <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="3f36e-168">Действия недоступны через [обычные маршруты](xref:mvc/controllers/routing#conventional-routing), определяемые <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> или <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="3f36e-169">Автоматические отклики HTTP 400</span><span class="sxs-lookup"><span data-stu-id="3f36e-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="3f36e-170">Благодаря атрибуту `[ApiController]` ошибки проверки модели автоматически активируют отклик HTTP 400.</span><span class="sxs-lookup"><span data-stu-id="3f36e-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="3f36e-171">В результате следующий код ненужен в методе действия:</span><span class="sxs-lookup"><span data-stu-id="3f36e-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="3f36e-172">Для выполнения предыдущей проверки ASP.NET Core MVC использует фильтр действий <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="3f36e-173">Отклик BadRequest по умолчанию</span><span class="sxs-lookup"><span data-stu-id="3f36e-173">Default BadRequest response</span></span>

<span data-ttu-id="3f36e-174">Если задана версия совместимости 2.1, для ответов HTTP 400 по умолчанию возвращается тип отклика <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="3f36e-175">Следующий текст запроса является примером сериализованного типа:</span><span class="sxs-lookup"><span data-stu-id="3f36e-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3f36e-176">Если задана версия совместимости 2.2 или более поздние версии, для ответов HTTP 400 по умолчанию возвращается тип отклика <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="3f36e-177">Следующий текст запроса является примером сериализованного типа:</span><span class="sxs-lookup"><span data-stu-id="3f36e-177">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="3f36e-178">Тип `ValidationProblemDetails`:</span><span class="sxs-lookup"><span data-stu-id="3f36e-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="3f36e-179">предоставляет распознаваемый компьютером формат для указания ошибок в откликах веб-API;</span><span class="sxs-lookup"><span data-stu-id="3f36e-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="3f36e-180">соответствует [спецификации RFC 7807](https://tools.ietf.org/html/rfc7807).</span><span class="sxs-lookup"><span data-stu-id="3f36e-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

<span data-ttu-id="3f36e-181">Чтобы обеспечить согласованность автоматических и настраиваемых ответов, вызовите метод <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A>, а не <xref:System.Web.Http.ApiController.BadRequest%2A>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-181">To make automatic and custom responses consistent, call the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A> method instead of <xref:System.Web.Http.ApiController.BadRequest%2A>.</span></span> <span data-ttu-id="3f36e-182">`ValidationProblem` возвращает объект <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>, а также автоматический ответ.</span><span class="sxs-lookup"><span data-stu-id="3f36e-182">`ValidationProblem` returns a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> object as well as the automatic response.</span></span>

### <a name="log-automatic-400-responses"></a><span data-ttu-id="3f36e-183">Запись в журнал автоматических откликов HTTP 400</span><span class="sxs-lookup"><span data-stu-id="3f36e-183">Log automatic 400 responses</span></span>

<span data-ttu-id="3f36e-184">См. описание проблемы [записи в журнал автоматических ответов HTTP 400 для ошибок проверки модели (aspnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="3f36e-184">See [How to log automatic 400 responses on model validation errors (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="3f36e-185">Отключение автоматической активации отклика HTTP 400</span><span class="sxs-lookup"><span data-stu-id="3f36e-185">Disable automatic 400 response</span></span>

<span data-ttu-id="3f36e-186">Чтобы отключить автоматическую активацию отклика HTTP 400, задайте свойству <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-186">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="3f36e-187">Добавьте выделенный ниже код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f36e-187">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="3f36e-188">Вывод параметров источника привязки</span><span class="sxs-lookup"><span data-stu-id="3f36e-188">Binding source parameter inference</span></span>

<span data-ttu-id="3f36e-189">Атрибут источника привязки определяет расположение, в котором находится значение параметра действия.</span><span class="sxs-lookup"><span data-stu-id="3f36e-189">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="3f36e-190">Существуют следующие атрибуты источника привязки.</span><span class="sxs-lookup"><span data-stu-id="3f36e-190">The following binding source attributes exist:</span></span>

|<span data-ttu-id="3f36e-191">Атрибут</span><span class="sxs-lookup"><span data-stu-id="3f36e-191">Attribute</span></span>|<span data-ttu-id="3f36e-192">Источник привязки</span><span class="sxs-lookup"><span data-stu-id="3f36e-192">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="3f36e-193">Текст запроса</span><span class="sxs-lookup"><span data-stu-id="3f36e-193">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="3f36e-194">Данные формы в тексте запроса</span><span class="sxs-lookup"><span data-stu-id="3f36e-194">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="3f36e-195">Заголовок запроса</span><span class="sxs-lookup"><span data-stu-id="3f36e-195">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="3f36e-196">Параметры строки запроса для запроса</span><span class="sxs-lookup"><span data-stu-id="3f36e-196">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="3f36e-197">Данные маршрута из текущего запроса</span><span class="sxs-lookup"><span data-stu-id="3f36e-197">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="3f36e-198">Служба запросов, внедренная в качестве параметра действия</span><span class="sxs-lookup"><span data-stu-id="3f36e-198">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="3f36e-199">Не используйте `[FromRoute]`, если значения могут содержать `%2f` (то есть `/`).</span><span class="sxs-lookup"><span data-stu-id="3f36e-199">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="3f36e-200">Для `%2f` не будет применяться отмена экранирования `/`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-200">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="3f36e-201">Используйте `[FromQuery]`, если значение может содержать `%2f`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-201">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="3f36e-202">Без атрибута `[ApiController]` или атрибутов источника привязки, таких как `[FromQuery]`, среда выполнения ASP.NET Core попытается использовать связыватель модели для составного объекта.</span><span class="sxs-lookup"><span data-stu-id="3f36e-202">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="3f36e-203">Связыватель модели для составного объекта извлекает данные из поставщиков значений в определенном порядке.</span><span class="sxs-lookup"><span data-stu-id="3f36e-203">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="3f36e-204">В следующем примере атрибут `[FromQuery]` указывает, что значение параметра `discontinuedOnly` задано в строке запроса URL-адреса для запроса:</span><span class="sxs-lookup"><span data-stu-id="3f36e-204">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="3f36e-205">Атрибут `[ApiController]` применяет правила зависимости к источникам данных по умолчанию для параметров действий.</span><span class="sxs-lookup"><span data-stu-id="3f36e-205">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="3f36e-206">Эти правила избавляют от необходимости вручную определять источники привязки путем применения атрибутов к параметрам действий.</span><span class="sxs-lookup"><span data-stu-id="3f36e-206">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="3f36e-207">Правила зависимости источника привязки работают следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3f36e-207">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="3f36e-208">`[FromBody]` выводится для параметров сложного типа.</span><span class="sxs-lookup"><span data-stu-id="3f36e-208">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="3f36e-209">Исключением из правила зависимости `[FromBody]` является любой сложный встроенный тип со специальным значением, такой как <xref:Microsoft.AspNetCore.Http.IFormCollection> и <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-209">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="3f36e-210">Код определения источника привязки игнорирует эти особые типы.</span><span class="sxs-lookup"><span data-stu-id="3f36e-210">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="3f36e-211">`[FromForm]` выводится для параметров действия с типом <xref:Microsoft.AspNetCore.Http.IFormFile> и <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-211">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="3f36e-212">Он не выводится ни для каких простых или определяемых пользователем типов.</span><span class="sxs-lookup"><span data-stu-id="3f36e-212">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="3f36e-213">`[FromRoute]` выводится для любого имени параметра действия, соответствующего параметру в шаблоне маршрута.</span><span class="sxs-lookup"><span data-stu-id="3f36e-213">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="3f36e-214">Если параметру действия соответствуют несколько маршрутов, любое значение маршрута рассматривается как `[FromRoute]`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-214">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="3f36e-215">`[FromQuery]` выводится для любых других параметров действия.</span><span class="sxs-lookup"><span data-stu-id="3f36e-215">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="3f36e-216">Заметки о выводе FromBody</span><span class="sxs-lookup"><span data-stu-id="3f36e-216">FromBody inference notes</span></span>

<span data-ttu-id="3f36e-217">`[FromBody]` не определен для простых типов, таких как `string` или `int`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-217">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="3f36e-218">Таким образом, атрибут `[FromBody]` должен использоваться для простых типов, когда требуются эти функции.</span><span class="sxs-lookup"><span data-stu-id="3f36e-218">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="3f36e-219">Если у действия более одного параметра для привязки из текста запроса, выдается исключение.</span><span class="sxs-lookup"><span data-stu-id="3f36e-219">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="3f36e-220">Например, все следующие сигнатуры метода действия вызывают исключение:</span><span class="sxs-lookup"><span data-stu-id="3f36e-220">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="3f36e-221">`[FromBody]` выводится для обеих параметров, так как они являются сложными типами.</span><span class="sxs-lookup"><span data-stu-id="3f36e-221">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="3f36e-222">Атрибут `[FromBody]`, заданный одному параметру, выводится для другого, так как это сложный тип.</span><span class="sxs-lookup"><span data-stu-id="3f36e-222">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="3f36e-223">Атрибут `[FromBody]` выводится для обоих параметров.</span><span class="sxs-lookup"><span data-stu-id="3f36e-223">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="3f36e-224">В ASP.NET Core 2.1 параметры типа коллекции, такие как списки и массивы, ошибочно выводятся как `[FromQuery]`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-224">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="3f36e-225">Для этих параметров следует использовать атрибут `[FromBody]`, если они должны быть привязаны из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="3f36e-225">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="3f36e-226">Это поведение, при котором параметры типа коллекции выводятся для привязки из текста по умолчанию, исправлено в версии ASP.NET Core, начиная с 2.2.</span><span class="sxs-lookup"><span data-stu-id="3f36e-226">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="3f36e-227">Отключение правил зависимости</span><span class="sxs-lookup"><span data-stu-id="3f36e-227">Disable inference rules</span></span>

<span data-ttu-id="3f36e-228">Чтобы отключить вывод источника привязки, задайте <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-228">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="3f36e-229">Добавьте следующий код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f36e-229">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="3f36e-230">Вывод многокомпонентных запросов и запросов данных форм</span><span class="sxs-lookup"><span data-stu-id="3f36e-230">Multipart/form-data request inference</span></span>

<span data-ttu-id="3f36e-231">Атрибут `[ApiController]` позволяет применить правило зависимости, когда параметр действия аннотирован атрибутом [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute).</span><span class="sxs-lookup"><span data-stu-id="3f36e-231">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="3f36e-232">При этом выводится тип содержимого запроса `multipart/form-data`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-232">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="3f36e-233">Чтобы отключить поведение по умолчанию, задайте свойству <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> значение `true` в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f36e-233">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

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

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="3f36e-234">Сведения о проблемах для кодов состояния ошибки</span><span class="sxs-lookup"><span data-stu-id="3f36e-234">Problem details for error status codes</span></span>

<span data-ttu-id="3f36e-235">Если задана версия совместимости, начиная с 2.2, MVC преобразовывает код ошибки (код состояния 400 и далее) в результат с <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span><span class="sxs-lookup"><span data-stu-id="3f36e-235">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="3f36e-236">Тип `ProblemDetails` основан на [спецификации RFC 7807](https://tools.ietf.org/html/rfc7807) и предоставляет считываемые компьютером сведения об ошибке в HTTP-ответе.</span><span class="sxs-lookup"><span data-stu-id="3f36e-236">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="3f36e-237">Рассмотрим следующий код в действии контроллера:</span><span class="sxs-lookup"><span data-stu-id="3f36e-237">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="3f36e-238">Метод `NotFound` создает код состояния HTTP 404 с текстом `ProblemDetails`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-238">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="3f36e-239">Пример:</span><span class="sxs-lookup"><span data-stu-id="3f36e-239">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="3f36e-240">Отключение ответа ProblemDetails</span><span class="sxs-lookup"><span data-stu-id="3f36e-240">Disable ProblemDetails response</span></span>

<span data-ttu-id="3f36e-241">Отключить автоматическое создание `ProblemDetails` для кодов состояния ошибок можно, задав свойству <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-241">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="3f36e-242">Добавьте следующий код в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3f36e-242">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="3f36e-243">Определение поддерживаемых типов содержимого запросов с помощью атрибута [Consumes]</span><span class="sxs-lookup"><span data-stu-id="3f36e-243">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="3f36e-244">По умолчанию действие поддерживает все доступные типы содержимого запросов.</span><span class="sxs-lookup"><span data-stu-id="3f36e-244">By default, an action supports all available request content types.</span></span> <span data-ttu-id="3f36e-245">Например, если в приложении включена поддержка [форматировщиков входных данных](xref:mvc/models/model-binding#input-formatters) JSON и XML, действие будет поддерживать несколько типов содержимого, включая `application/json` и `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-245">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="3f36e-246">Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов.</span><span class="sxs-lookup"><span data-stu-id="3f36e-246">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="3f36e-247">Примените атрибут `[Consumes]` к действию или контроллеру, указав один или несколько типов содержимого:</span><span class="sxs-lookup"><span data-stu-id="3f36e-247">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="3f36e-248">В приведенном выше коде действие `CreateProduct` указывает тип содержимого `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-248">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="3f36e-249">Запросы, направляемые в это действие, должны определять заголовок `Content-Type` `application/xml`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-249">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="3f36e-250">Запросы, не определяющие заголовок `Content-Type` `application/xml`, возвращают ответ [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) (неподдерживаемый тип данных).</span><span class="sxs-lookup"><span data-stu-id="3f36e-250">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="3f36e-251">Атрибут `[Consumes]` также позволяет действию влиять на выбор с учетом типа содержимого входящего запроса, применяя ограничение типа.</span><span class="sxs-lookup"><span data-stu-id="3f36e-251">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="3f36e-252">Рассмотрим следующий пример.</span><span class="sxs-lookup"><span data-stu-id="3f36e-252">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="3f36e-253">В приведенном выше коде для `ConsumesController` включена поддержка обработки запросов, отправляемых на URL-адрес `https://localhost:5001/api/Consumes`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-253">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="3f36e-254">Оба действия контроллера (`PostJson` и `PostForm`) обрабатывают запросы POST с одним и тем же URL-адресом.</span><span class="sxs-lookup"><span data-stu-id="3f36e-254">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="3f36e-255">Если в атрибуте `[Consumes]` не применяется ограничение типа, возникает исключение неоднозначного соответствия.</span><span class="sxs-lookup"><span data-stu-id="3f36e-255">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="3f36e-256">Атрибут `[Consumes]` применяется к обоим действиям.</span><span class="sxs-lookup"><span data-stu-id="3f36e-256">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="3f36e-257">Действие `PostJson` обрабатывает запросы, отправленные с заголовком `Content-Type` `application/json`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-257">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="3f36e-258">Действие `PostForm` обрабатывает запросы, отправленные с заголовком `Content-Type` `application/x-www-form-urlencoded`.</span><span class="sxs-lookup"><span data-stu-id="3f36e-258">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="3f36e-259">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3f36e-259">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
* [<span data-ttu-id="3f36e-260">Microsoft Learn. Создание веб-API с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f36e-260">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
