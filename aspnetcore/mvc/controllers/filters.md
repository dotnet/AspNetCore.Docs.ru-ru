---
title: Фильтры в ASP.NET Core
author: Rick-Anderson
description: Из этой статьи вы узнаете, как работают фильтры и как их использовать в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: b53b017e63ada62438e352a6c5112b7584ff0b26
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589105"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="50e5d-103">Фильтры в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50e5d-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="50e5d-104">Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="50e5d-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="50e5d-105">*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="50e5d-106">Встроенные фильтры обрабатывают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="50e5d-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="50e5d-107">Авторизация (предотвращение несанкционированного доступа к ресурсам).</span><span class="sxs-lookup"><span data-stu-id="50e5d-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="50e5d-108">Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).</span><span class="sxs-lookup"><span data-stu-id="50e5d-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="50e5d-109">Для обработки сквозной функциональности можно создавать настраиваемые фильтры.</span><span class="sxs-lookup"><span data-stu-id="50e5d-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="50e5d-110">Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="50e5d-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="50e5d-111">Фильтры предотвращают дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="50e5d-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="50e5d-112">Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="50e5d-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="50e5d-113">Этот документ применяется к Razor страницам, контроллерам API и контроллерам с представлениями.</span><span class="sxs-lookup"><span data-stu-id="50e5d-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="50e5d-114">Фильтры не работают непосредственно с [ Razor компонентами](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="50e5d-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="50e5d-115">Фильтр может влиять на компонент только косвенно в таких случаях:</span><span class="sxs-lookup"><span data-stu-id="50e5d-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="50e5d-116">Компонент внедряется в страницу или представление.</span><span class="sxs-lookup"><span data-stu-id="50e5d-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="50e5d-117">Страница, контроллер или представление использует фильтр.</span><span class="sxs-lookup"><span data-stu-id="50e5d-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="50e5d-118">[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="50e5d-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="50e5d-119">Как работают фильтры</span><span class="sxs-lookup"><span data-stu-id="50e5d-119">How filters work</span></span>

<span data-ttu-id="50e5d-120">Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.</span><span class="sxs-lookup"><span data-stu-id="50e5d-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="50e5d-121">Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.</span><span class="sxs-lookup"><span data-stu-id="50e5d-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Запрос обрабатывается с помощью прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="50e5d-124">Типы фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-124">Filter types</span></span>

<span data-ttu-id="50e5d-125">Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:</span><span class="sxs-lookup"><span data-stu-id="50e5d-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="50e5d-126">[Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="50e5d-127">Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.</span><span class="sxs-lookup"><span data-stu-id="50e5d-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="50e5d-128">[Фильтры ресурсов](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="50e5d-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="50e5d-129">Выполняются после авторизации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-129">Run after authorization.</span></span>  
  * <span data-ttu-id="50e5d-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> выполняет код до остальной части конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="50e5d-131">Например, `OnResourceExecuting` выполняет код до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="50e5d-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="50e5d-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> выполняет код после завершения остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="50e5d-133">[Фильтры действий](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="50e5d-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="50e5d-134">выполняют код непосредственно до и после вызова метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="50e5d-135">Могут изменять аргументы, передаваемые в действие.</span><span class="sxs-lookup"><span data-stu-id="50e5d-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="50e5d-136">Могут изменять результат, возвращенный действием.</span><span class="sxs-lookup"><span data-stu-id="50e5d-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="50e5d-137">**Не** поддерживаются на Razor страницах.</span><span class="sxs-lookup"><span data-stu-id="50e5d-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="50e5d-138">[Фильтры исключений](#exception-filters) применяют глобальные политики к необработанным исключениям, которые происходят до записи данных в тело ответа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="50e5d-139">[Фильтры результатов](#result-filters) выполняют код непосредственно до и после выполнения результатов действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="50e5d-140">Они выполняются только в том случае, если метод действия выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="50e5d-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="50e5d-141">Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="50e5d-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="50e5d-142">На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="50e5d-145">Реализация</span><span class="sxs-lookup"><span data-stu-id="50e5d-145">Implementation</span></span>

<span data-ttu-id="50e5d-146">Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="50e5d-147">Синхронные фильтры выполняют код до и после этапа конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="50e5d-148">Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> вызывается перед вызовом метода действия,</span><span class="sxs-lookup"><span data-stu-id="50e5d-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="50e5d-149">а <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> — после возврата метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="50e5d-150">В приведенном выше коде [мидебуг](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) — это служебная функция в [примере загрузки](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="50e5d-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="50e5d-151">Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="50e5d-152">Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="50e5d-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="50e5d-153">В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="50e5d-154">Несколько этапов фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-154">Multiple filter stages</span></span>

<span data-ttu-id="50e5d-155">Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе.</span><span class="sxs-lookup"><span data-stu-id="50e5d-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="50e5d-156">Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует следующие интерфейсы:</span><span class="sxs-lookup"><span data-stu-id="50e5d-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="50e5d-157">синхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>;</span><span class="sxs-lookup"><span data-stu-id="50e5d-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="50e5d-158">асинхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="50e5d-159">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="50e5d-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="50e5d-160">Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он.</span><span class="sxs-lookup"><span data-stu-id="50e5d-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="50e5d-161">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="50e5d-162">Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод.</span><span class="sxs-lookup"><span data-stu-id="50e5d-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="50e5d-163">При использовании абстрактных классов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , таких как, переопределяйте только синхронные методы или асинхронные методы для каждого типа фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="50e5d-164">Встроенные атрибуты фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-164">Built-in filter attributes</span></span>

<span data-ttu-id="50e5d-165">ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="50e5d-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="50e5d-166">Например, следующий фильтр результатов добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="50e5d-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="50e5d-167">Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше.</span><span class="sxs-lookup"><span data-stu-id="50e5d-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="50e5d-168">Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:</span><span class="sxs-lookup"><span data-stu-id="50e5d-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="50e5d-169">Для изучения заголовков используйте средство [разработчика браузера](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="50e5d-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="50e5d-170">В **заголовке ответа** отображается `author: Rick Anderson`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="50e5d-171">В следующем коде реализуется класс `ActionFilterAttribute`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="50e5d-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="50e5d-172">Считывает заголовок и имя в системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="50e5d-173">В отличие от предыдущего примера, для указанного ниже кода не нужно добавлять параметры фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="50e5d-174">Добавляет заголовок и имя к заголовку ответа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="50e5d-175">Параметры конфигурации предоставляются из [системы конфигурации](xref:fundamentals/configuration/index) с помощью [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="50e5d-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="50e5d-176">Например, из *appsettings.json* файла:</span><span class="sxs-lookup"><span data-stu-id="50e5d-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="50e5d-177">В `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="50e5d-178">Класс `PositionOptions` добавляется в контейнер службы с помощью области конфигурации `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="50e5d-179">`MyActionFilterAttribute` добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="50e5d-180">В следующем коде демонстрируется класс `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="50e5d-181">В следующем коде применяется атрибут `MyActionFilterAttribute` к методу `Index2`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="50e5d-182">В разделе **заголовки ответов**, `author: Rick Anderson` и `Editor: Joe Smith` отображается при `Sample/Index2` вызове конечной точки.</span><span class="sxs-lookup"><span data-stu-id="50e5d-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="50e5d-183">Следующий код применяет `MyActionFilterAttribute` и `AddHeaderAttribute` к Razor странице:</span><span class="sxs-lookup"><span data-stu-id="50e5d-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="50e5d-184">Фильтры не могут применяться к Razor методам обработчика страниц.</span><span class="sxs-lookup"><span data-stu-id="50e5d-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="50e5d-185">Их можно применять либо к Razor модели страницы, либо глобально.</span><span class="sxs-lookup"><span data-stu-id="50e5d-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="50e5d-186">Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.</span><span class="sxs-lookup"><span data-stu-id="50e5d-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="50e5d-187">Атрибуты фильтров:</span><span class="sxs-lookup"><span data-stu-id="50e5d-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="50e5d-188">Области и порядок выполнения фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="50e5d-189">Фильтр можно добавить в конвейер в одной из трех *областей*:</span><span class="sxs-lookup"><span data-stu-id="50e5d-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="50e5d-190">С помощью атрибута в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="50e5d-191">Атрибуты фильтра не могут применяться к Razor методам обработчика страниц.</span><span class="sxs-lookup"><span data-stu-id="50e5d-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="50e5d-192">Использование атрибута на контроллере или Razor странице.</span><span class="sxs-lookup"><span data-stu-id="50e5d-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="50e5d-193">Глобально для всех контроллеров, действий и Razor страниц, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="50e5d-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="50e5d-194">Порядок выполнения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="50e5d-194">Default order of execution</span></span>

<span data-ttu-id="50e5d-195">Если для определенного этапа конвейера имеется несколько фильтров, область определяет порядок их выполнения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="50e5d-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="50e5d-196">Глобальные фильтры заключают в себя фильтры классов, которые, в свою очередь, заключают в себя фильтры методов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="50e5d-197">В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода.</span><span class="sxs-lookup"><span data-stu-id="50e5d-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="50e5d-198">Последовательность фильтров:</span><span class="sxs-lookup"><span data-stu-id="50e5d-198">The filter sequence:</span></span>

* <span data-ttu-id="50e5d-199">*Предшествующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="50e5d-200">Код, *предшествующий* контроллеру и Razor фильтрам страниц.</span><span class="sxs-lookup"><span data-stu-id="50e5d-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="50e5d-201">*Предшествующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="50e5d-202">*Последующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="50e5d-203">Код *после* кода фильтров контроллеров и Razor страниц.</span><span class="sxs-lookup"><span data-stu-id="50e5d-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="50e5d-204">*Последующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="50e5d-205">В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="50e5d-206">Последовательность</span><span class="sxs-lookup"><span data-stu-id="50e5d-206">Sequence</span></span> | <span data-ttu-id="50e5d-207">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-207">Filter scope</span></span> | <span data-ttu-id="50e5d-208">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="50e5d-209">1</span><span class="sxs-lookup"><span data-stu-id="50e5d-209">1</span></span> | <span data-ttu-id="50e5d-210">Глобальный</span><span class="sxs-lookup"><span data-stu-id="50e5d-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="50e5d-211">2</span><span class="sxs-lookup"><span data-stu-id="50e5d-211">2</span></span> | <span data-ttu-id="50e5d-212">Контроллер или Razor страница</span><span class="sxs-lookup"><span data-stu-id="50e5d-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="50e5d-213">3</span><span class="sxs-lookup"><span data-stu-id="50e5d-213">3</span></span> | <span data-ttu-id="50e5d-214">Метод</span><span class="sxs-lookup"><span data-stu-id="50e5d-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="50e5d-215">4</span><span class="sxs-lookup"><span data-stu-id="50e5d-215">4</span></span> | <span data-ttu-id="50e5d-216">Метод</span><span class="sxs-lookup"><span data-stu-id="50e5d-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="50e5d-217">5</span><span class="sxs-lookup"><span data-stu-id="50e5d-217">5</span></span> | <span data-ttu-id="50e5d-218">Контроллер или Razor страница</span><span class="sxs-lookup"><span data-stu-id="50e5d-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="50e5d-219">6</span><span class="sxs-lookup"><span data-stu-id="50e5d-219">6</span></span> | <span data-ttu-id="50e5d-220">Глобальный</span><span class="sxs-lookup"><span data-stu-id="50e5d-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="50e5d-221">Фильтры на уровне контроллера</span><span class="sxs-lookup"><span data-stu-id="50e5d-221">Controller level filters</span></span>

<span data-ttu-id="50e5d-222">Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="50e5d-223">Эти методы:</span><span class="sxs-lookup"><span data-stu-id="50e5d-223">These methods:</span></span>

* <span data-ttu-id="50e5d-224">Заключают фильтры, которые выполняются для указанного действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="50e5d-225">`OnActionExecuting` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="50e5d-226">`OnActionExecuted` вызывается после всех фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="50e5d-227">`OnActionExecutionAsync` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="50e5d-228">Код в фильтре после `next` выполняется после метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="50e5d-229">Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.</span><span class="sxs-lookup"><span data-stu-id="50e5d-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="50e5d-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-230">The `TestController`:</span></span>

* <span data-ttu-id="50e5d-231">Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="50e5d-232">Переопределяет `OnActionExecuting` и `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="50e5d-233">Переходит к `https://localhost:5001/Test/FilterTest2` для выполнения следующего кода:</span><span class="sxs-lookup"><span data-stu-id="50e5d-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="50e5d-234">Фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="50e5d-235">Их **нельзя** настроить, чтобы они запускались после применения фильтров к методам.</span><span class="sxs-lookup"><span data-stu-id="50e5d-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="50e5d-236">Свойство Order рассматривается в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="50e5d-236">Order is explained in the next section.</span></span>

<span data-ttu-id="50e5d-237">Для Razor страниц см. раздел [реализация Razor фильтров страниц путем переопределения методов фильтров](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="50e5d-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="50e5d-238">Переопределение порядка по умолчанию</span><span class="sxs-lookup"><span data-stu-id="50e5d-238">Overriding the default order</span></span>

<span data-ttu-id="50e5d-239">Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="50e5d-240">`IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения.</span><span class="sxs-lookup"><span data-stu-id="50e5d-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="50e5d-241">Фильтр со значением меньше `Order`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="50e5d-242">Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="50e5d-243">Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="50e5d-244">Свойство `Order` задается с помощью параметра конструктора:</span><span class="sxs-lookup"><span data-stu-id="50e5d-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="50e5d-245">Рассмотрим два фильтра действий в следующем контроллере:</span><span class="sxs-lookup"><span data-stu-id="50e5d-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="50e5d-246">Глобальный фильтр добавляется в `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="50e5d-247">3 фильтра выполняются в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="50e5d-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="50e5d-248">Свойство `Order` переопределяет область при определении порядка выполнения фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="50e5d-249">Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области.</span><span class="sxs-lookup"><span data-stu-id="50e5d-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="50e5d-250">Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0.</span><span class="sxs-lookup"><span data-stu-id="50e5d-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="50e5d-251">Как упоминалось ранее, фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`. Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="50e5d-252">В указанном выше коде `MySampleActionFilter` имеет глобальную область действия и запускается перед `MyAction2FilterAttribute`, у которого область действия контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="50e5d-253">Чтобы `MyAction2FilterAttribute` запускался первым, задайте для свойства Order значение `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="50e5d-254">Чтобы глобальный фильтр `MySampleActionFilter` запускался первым, задайте для свойства `Order` значение `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="50e5d-255">Отмена и сокращенное выполнение</span><span class="sxs-lookup"><span data-stu-id="50e5d-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="50e5d-256">Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="50e5d-257">Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:</span><span class="sxs-lookup"><span data-stu-id="50e5d-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="50e5d-258">В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="50e5d-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="50e5d-260">Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="50e5d-261">Замыкает оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="50e5d-262">Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="50e5d-263">Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="50e5d-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="50e5d-264">Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=1,15)]

## <a name="dependency-injection"></a><span data-ttu-id="50e5d-265">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="50e5d-265">Dependency injection</span></span>

<span data-ttu-id="50e5d-266">Фильтры можно добавлять по типу или экземпляру.</span><span class="sxs-lookup"><span data-stu-id="50e5d-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="50e5d-267">Добавляемый экземпляр используется для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="50e5d-268">Если добавляется тип, он является активированным.</span><span class="sxs-lookup"><span data-stu-id="50e5d-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="50e5d-269">Активация фильтра означает, что:</span><span class="sxs-lookup"><span data-stu-id="50e5d-269">A type-activated filter means:</span></span>

* <span data-ttu-id="50e5d-270">Экземпляр создается для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-270">An instance is created for each request.</span></span>
* <span data-ttu-id="50e5d-271">Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="50e5d-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="50e5d-272">Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="50e5d-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="50e5d-273">Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:</span><span class="sxs-lookup"><span data-stu-id="50e5d-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="50e5d-274">Параметры конструктора должны предоставляться для атрибутов в месте их применения.</span><span class="sxs-lookup"><span data-stu-id="50e5d-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="50e5d-275">Это ограничение, налагаемое на использование атрибутов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="50e5d-276">Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="50e5d-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="50e5d-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="50e5d-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="50e5d-278">Предыдущие фильтры могут применяться к контроллеру или методу действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="50e5d-279">Средства ведения журнала доступны путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-279">Loggers are available from DI.</span></span> <span data-ttu-id="50e5d-280">Но следует избегать создания и использования фильтров исключительно для целей ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="50e5d-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="50e5d-281">[Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="50e5d-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="50e5d-282">При добавлении ведения журналов в фильтры следует учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="50e5d-282">Logging added to filters:</span></span>

* <span data-ttu-id="50e5d-283">Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.</span><span class="sxs-lookup"><span data-stu-id="50e5d-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="50e5d-284">**Не** следует регистрировать действия или другие события платформы,</span><span class="sxs-lookup"><span data-stu-id="50e5d-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="50e5d-285">так как это делают встроенные фильтры.</span><span class="sxs-lookup"><span data-stu-id="50e5d-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="50e5d-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="50e5d-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="50e5d-287">Типы реализации фильтра службы регистрируются в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="50e5d-288">Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="50e5d-289">В следующем коде используется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="50e5d-290">В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="50e5d-291">В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="50e5d-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="50e5d-292">При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="50e5d-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="50e5d-293">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="50e5d-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="50e5d-294">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="50e5d-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="50e5d-295">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="50e5d-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="50e5d-296">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="50e5d-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="50e5d-297">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="50e5d-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="50e5d-298">Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="50e5d-299">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="50e5d-300">`CreateInstance` загружает указанный тип из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="50e5d-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="50e5d-301">TypeFilterAttribute</span></span>

<span data-ttu-id="50e5d-302">Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="50e5d-303">Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="50e5d-304">Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="50e5d-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="50e5d-305">Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="50e5d-306">Их зависимости выполняются самим контейнером.</span><span class="sxs-lookup"><span data-stu-id="50e5d-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="50e5d-307">Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="50e5d-308">При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="50e5d-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="50e5d-309">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="50e5d-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="50e5d-310">Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="50e5d-311">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="50e5d-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="50e5d-312">В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="50e5d-313">Фильтры авторизации</span><span class="sxs-lookup"><span data-stu-id="50e5d-313">Authorization filters</span></span>

<span data-ttu-id="50e5d-314">Фильтры авторизации:</span><span class="sxs-lookup"><span data-stu-id="50e5d-314">Authorization filters:</span></span>

* <span data-ttu-id="50e5d-315">Выполняются в первую очередь в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="50e5d-316">Контролируют доступ к методам действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-316">Control access to action methods.</span></span>
* <span data-ttu-id="50e5d-317">Имеют предшествующий, но не последующий метод.</span><span class="sxs-lookup"><span data-stu-id="50e5d-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="50e5d-318">Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="50e5d-319">Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="50e5d-320">Встроенный фильтр авторизации:</span><span class="sxs-lookup"><span data-stu-id="50e5d-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="50e5d-321">Вызывает систему авторизации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-321">Calls the authorization system.</span></span>
* <span data-ttu-id="50e5d-322">Не выполняет авторизацию запросов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-322">Does not authorize requests.</span></span>

<span data-ttu-id="50e5d-323">**Не** вызывайте исключения в фильтрах авторизации:</span><span class="sxs-lookup"><span data-stu-id="50e5d-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="50e5d-324">Исключение не будет обработано.</span><span class="sxs-lookup"><span data-stu-id="50e5d-324">The exception will not be handled.</span></span>
* <span data-ttu-id="50e5d-325">Фильтры исключений не будут обрабатывать исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="50e5d-326">При возникновении исключения в фильтре авторизации попробуйте создать запрос.</span><span class="sxs-lookup"><span data-stu-id="50e5d-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="50e5d-327">Дополнительные сведения об [авторизации](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="50e5d-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="50e5d-328">Фильтры ресурсов</span><span class="sxs-lookup"><span data-stu-id="50e5d-328">Resource filters</span></span>

<span data-ttu-id="50e5d-329">Фильтры ресурсов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-329">Resource filters:</span></span>

* <span data-ttu-id="50e5d-330">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="50e5d-331">Выполнение охватывает большую часть конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="50e5d-332">До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).</span><span class="sxs-lookup"><span data-stu-id="50e5d-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="50e5d-333">Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="50e5d-334">Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.</span><span class="sxs-lookup"><span data-stu-id="50e5d-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="50e5d-335">Примеры фильтров ресурсов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-335">Resource filter examples:</span></span>

* <span data-ttu-id="50e5d-336">[Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.</span><span class="sxs-lookup"><span data-stu-id="50e5d-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="50e5d-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="50e5d-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="50e5d-338">Предотвращает доступ привязки модели к данным формы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="50e5d-339">Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.</span><span class="sxs-lookup"><span data-stu-id="50e5d-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="50e5d-340">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="50e5d-340">Action filters</span></span>

<span data-ttu-id="50e5d-341">Фильтры действий **не** применяются к Razor страницам.</span><span class="sxs-lookup"><span data-stu-id="50e5d-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="50e5d-342">Razor Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="50e5d-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="50e5d-343">Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="50e5d-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="50e5d-344">Фильтры действий:</span><span class="sxs-lookup"><span data-stu-id="50e5d-344">Action filters:</span></span>

* <span data-ttu-id="50e5d-345">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="50e5d-346">Их выполнение охватывает выполнение методов действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="50e5d-347">В следующем фрагменте кода показан пример фильтра действий:</span><span class="sxs-lookup"><span data-stu-id="50e5d-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="50e5d-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="50e5d-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="50e5d-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="50e5d-350"><xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="50e5d-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="50e5d-352">Вызов исключения в методе действия:</span><span class="sxs-lookup"><span data-stu-id="50e5d-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="50e5d-353">Предотвращает выполнение последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="50e5d-354">В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="50e5d-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="50e5d-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="50e5d-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="50e5d-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="50e5d-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="50e5d-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="50e5d-358">При присвоении этому свойству ненулевого значения:</span><span class="sxs-lookup"><span data-stu-id="50e5d-358">Setting this property to null:</span></span>

  * <span data-ttu-id="50e5d-359">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="50e5d-360">`Result` будет выполняться так, как при возвращении методом действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="50e5d-361">Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="50e5d-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="50e5d-362">Приводит к выполнению последующих фильтров действий и метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="50e5d-363">Возвращает `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="50e5d-364">Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="50e5d-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="50e5d-365">Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="50e5d-366">Фильтр действий `OnActionExecuting` можно использовать:</span><span class="sxs-lookup"><span data-stu-id="50e5d-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="50e5d-367">Для проверки состояния модели.</span><span class="sxs-lookup"><span data-stu-id="50e5d-367">Validate model state.</span></span>
* <span data-ttu-id="50e5d-368">Для получения ошибки, если состояние является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="50e5d-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="50e5d-369">Контроллеры, отмеченные `[ApiController]` атрибутом, автоматически проверяют состояние модели и возвращают ответ 400.</span><span class="sxs-lookup"><span data-stu-id="50e5d-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="50e5d-370">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="50e5d-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="50e5d-371">Метод `OnActionExecuted` выполняется после метода действия:</span><span class="sxs-lookup"><span data-stu-id="50e5d-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="50e5d-372">Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="50e5d-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="50e5d-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="50e5d-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="50e5d-375">Если установить для `Exception` значение NULL:</span><span class="sxs-lookup"><span data-stu-id="50e5d-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="50e5d-376">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="50e5d-377">`ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.</span><span class="sxs-lookup"><span data-stu-id="50e5d-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="50e5d-378">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="50e5d-378">Exception filters</span></span>

<span data-ttu-id="50e5d-379">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-379">Exception filters:</span></span>

* <span data-ttu-id="50e5d-380">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="50e5d-381">Можно использовать для реализации политик обработки стандартных ошибок.</span><span class="sxs-lookup"><span data-stu-id="50e5d-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="50e5d-382">В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:</span><span class="sxs-lookup"><span data-stu-id="50e5d-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="50e5d-383">В следующем коде проверяется фильтр исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="50e5d-384">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-384">Exception filters:</span></span>

* <span data-ttu-id="50e5d-385">Не имеют предшествующих и последующих событий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-385">Don't have before and after events.</span></span>
* <span data-ttu-id="50e5d-386">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="50e5d-387">Обрабатывать необработанные исключения, происходящие при Razor создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="50e5d-388">**Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.</span><span class="sxs-lookup"><span data-stu-id="50e5d-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="50e5d-389">Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ.</span><span class="sxs-lookup"><span data-stu-id="50e5d-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="50e5d-390">Это предотвратит распространение исключения.</span><span class="sxs-lookup"><span data-stu-id="50e5d-390">This stops propagation of the exception.</span></span> <span data-ttu-id="50e5d-391">Фильтр исключений не может преобразовать исключение в успешное выполнение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="50e5d-392">Это может сделать только фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-392">Only an action filter can do that.</span></span>

<span data-ttu-id="50e5d-393">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-393">Exception filters:</span></span>

* <span data-ttu-id="50e5d-394">Хорошо подходят для перехвата исключений, возникающих в действиях.</span><span class="sxs-lookup"><span data-stu-id="50e5d-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="50e5d-395">Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="50e5d-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="50e5d-396">ПО промежуточного слоя хорошо подходит для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="50e5d-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="50e5d-397">Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="50e5d-398">Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML.</span><span class="sxs-lookup"><span data-stu-id="50e5d-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="50e5d-399">Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="50e5d-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="50e5d-400">Фильтры результатов</span><span class="sxs-lookup"><span data-stu-id="50e5d-400">Result filters</span></span>

<span data-ttu-id="50e5d-401">Фильтры результатов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-401">Result filters:</span></span>

* <span data-ttu-id="50e5d-402">Реализация интерфейса:</span><span class="sxs-lookup"><span data-stu-id="50e5d-402">Implement an interface:</span></span>
  * <span data-ttu-id="50e5d-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="50e5d-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="50e5d-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="50e5d-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="50e5d-405">Их выполнение охватывает выполнение результатов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="50e5d-406">IResultFilter и IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="50e5d-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="50e5d-407">В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:</span><span class="sxs-lookup"><span data-stu-id="50e5d-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="50e5d-408">Тип выполняемого результата зависит от соответствующего действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="50e5d-409">Действие, возвращающее представление, включает всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="50e5d-410">В процессе выполнения результата метод API может производить сериализацию.</span><span class="sxs-lookup"><span data-stu-id="50e5d-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="50e5d-411">Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="50e5d-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="50e5d-412">Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="50e5d-413">Фильтры результатов не выполняются в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="50e5d-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="50e5d-414">Фильтр авторизации или ресурсов выполняет сокращение конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="50e5d-415">Фильтр исключений обрабатывает исключение и выдает результат действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="50e5d-416">Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="50e5d-417">При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="50e5d-418">Создание исключения в `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="50e5d-419">предотвращает выполнение результата действия и последующих фильтров;</span><span class="sxs-lookup"><span data-stu-id="50e5d-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="50e5d-420">рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="50e5d-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="50e5d-421">Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту.</span><span class="sxs-lookup"><span data-stu-id="50e5d-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="50e5d-422">Если ответ уже был отправлен клиенту, его нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="50e5d-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="50e5d-423">`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="50e5d-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="50e5d-424">`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="50e5d-425">Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов на дальнейших этапах конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="50e5d-426">Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует.</span><span class="sxs-lookup"><span data-stu-id="50e5d-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="50e5d-427">Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.</span><span class="sxs-lookup"><span data-stu-id="50e5d-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="50e5d-428">Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="50e5d-429">Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="50e5d-430">Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="50e5d-431">Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="50e5d-432">IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="50e5d-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="50e5d-433">Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="50e5d-434">Сюда включены результаты действий, созданные:</span><span class="sxs-lookup"><span data-stu-id="50e5d-434">This includes action results produced by:</span></span>

* <span data-ttu-id="50e5d-435">фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;</span><span class="sxs-lookup"><span data-stu-id="50e5d-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="50e5d-436">фильтрами исключений.</span><span class="sxs-lookup"><span data-stu-id="50e5d-436">Exception filters.</span></span>

<span data-ttu-id="50e5d-437">Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:</span><span class="sxs-lookup"><span data-stu-id="50e5d-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

## <a name="ifilterfactory"></a><span data-ttu-id="50e5d-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="50e5d-438">IFilterFactory</span></span>

<span data-ttu-id="50e5d-439">Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="50e5d-440">Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="50e5d-441">Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="50e5d-442">Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="50e5d-443">Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="50e5d-444">`IFilterFactory.IsReusable`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-444">`IFilterFactory.IsReusable`:</span></span>

* <span data-ttu-id="50e5d-445">Является указанием фабрики, которую экземпляр фильтра, созданный фабрикой, может быть повторно использован вне области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="50e5d-445">Is a hint by the factory that the filter instance created by the factory may be reused outside of the request scope it was created within.</span></span>
* <span data-ttu-id="50e5d-446">***Не*** следует использовать с фильтром, зависящим от служб со временем существования, отличным от Singleton.</span><span class="sxs-lookup"><span data-stu-id="50e5d-446">Should ***not*** be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="50e5d-447">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="50e5d-447">The ASP.NET Core runtime doesn't guarantee:</span></span>

* <span data-ttu-id="50e5d-448">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="50e5d-448">That a single instance of the filter will be created.</span></span>
* <span data-ttu-id="50e5d-449">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="50e5d-449">The filter will not be re-requested from the DI container at some later point.</span></span>

> [!WARNING] 
> <span data-ttu-id="50e5d-450">Настраивается <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> для возврата только в том `true` случае, если источник фильтров является однозначным, фильтры не имеют состояния, а фильтры могут использоваться в нескольких HTTP-запросах.</span><span class="sxs-lookup"><span data-stu-id="50e5d-450">Only configure <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> to return `true` if the source of the filters is unambiguous, the filters are stateless, and the filters are safe to use across multiple HTTP requests.</span></span> <span data-ttu-id="50e5d-451">Например, не следует возвращать фильтры из DI, которые зарегистрированы как область или временное значение, если `IFilterFactory.IsReusable` возвращает `true` .</span><span class="sxs-lookup"><span data-stu-id="50e5d-451">For instance, don't return filters from DI that are registered as scoped or transient if `IFilterFactory.IsReusable` returns `true`.</span></span>

<span data-ttu-id="50e5d-452">Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-452">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="50e5d-453">В следующем коде применяется фильтр:</span><span class="sxs-lookup"><span data-stu-id="50e5d-453">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="50e5d-454">Проверьте приведенный выше код, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="50e5d-454">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="50e5d-455">Вызовите средства для разработчика (F12).</span><span class="sxs-lookup"><span data-stu-id="50e5d-455">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="50e5d-456">Перейдите к `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-456">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="50e5d-457">В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:</span><span class="sxs-lookup"><span data-stu-id="50e5d-457">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="50e5d-458">**Автор:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="50e5d-458">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="50e5d-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="50e5d-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="50e5d-460">**внутренний:**`My header`</span><span class="sxs-lookup"><span data-stu-id="50e5d-460">**internal:** `My header`</span></span>

<span data-ttu-id="50e5d-461">Приведенный выше код создает заголовок ответа **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-461">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="50e5d-462">Реализация IFilterFactory в атрибуте</span><span class="sxs-lookup"><span data-stu-id="50e5d-462">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="50e5d-463">Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:</span><span class="sxs-lookup"><span data-stu-id="50e5d-463">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="50e5d-464">Не требуют передачи параметров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-464">Don't require passing parameters.</span></span>
* <span data-ttu-id="50e5d-465">Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-465">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="50e5d-466">Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="50e5d-467">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-467">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="50e5d-468">`CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).</span><span class="sxs-lookup"><span data-stu-id="50e5d-468">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="50e5d-469">В коде ниже приведено три примера применения `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-469">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="50e5d-470">В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-470">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="50e5d-471">Использование ПО промежуточного слоя в конвейере фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-471">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="50e5d-472">Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере.</span><span class="sxs-lookup"><span data-stu-id="50e5d-472">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="50e5d-473">Однако фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения, а значит имеют доступ к контексту и конструкциям.</span><span class="sxs-lookup"><span data-stu-id="50e5d-473">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="50e5d-474">Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-474">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="50e5d-475">В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:</span><span class="sxs-lookup"><span data-stu-id="50e5d-475">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="50e5d-476">Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="50e5d-476">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="50e5d-477">Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-477">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="50e5d-478">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="50e5d-478">Next actions</span></span>

* <span data-ttu-id="50e5d-479">См. раздел [методы фильтров для Razor страниц](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="50e5d-479">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="50e5d-480">Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="50e5d-480">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="50e5d-481">Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="50e5d-481">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="50e5d-482">*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-482">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="50e5d-483">Встроенные фильтры обрабатывают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="50e5d-483">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="50e5d-484">Авторизация (предотвращение несанкционированного доступа к ресурсам).</span><span class="sxs-lookup"><span data-stu-id="50e5d-484">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="50e5d-485">Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).</span><span class="sxs-lookup"><span data-stu-id="50e5d-485">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="50e5d-486">Для обработки сквозной функциональности можно создавать настраиваемые фильтры.</span><span class="sxs-lookup"><span data-stu-id="50e5d-486">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="50e5d-487">Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="50e5d-487">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="50e5d-488">Фильтры предотвращают дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="50e5d-488">Filters avoid duplicating code.</span></span> <span data-ttu-id="50e5d-489">Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="50e5d-489">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="50e5d-490">Этот документ применяется к Razor страницам, контроллерам API и контроллерам с представлениями.</span><span class="sxs-lookup"><span data-stu-id="50e5d-490">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="50e5d-491">[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="50e5d-491">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="50e5d-492">Как работают фильтры</span><span class="sxs-lookup"><span data-stu-id="50e5d-492">How filters work</span></span>

<span data-ttu-id="50e5d-493">Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.</span><span class="sxs-lookup"><span data-stu-id="50e5d-493">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="50e5d-494">Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.</span><span class="sxs-lookup"><span data-stu-id="50e5d-494">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Запрос обрабатывается с использованием прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="50e5d-497">Типы фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-497">Filter types</span></span>

<span data-ttu-id="50e5d-498">Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:</span><span class="sxs-lookup"><span data-stu-id="50e5d-498">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="50e5d-499">[Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-499">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="50e5d-500">Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.</span><span class="sxs-lookup"><span data-stu-id="50e5d-500">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="50e5d-501">[Фильтры ресурсов](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="50e5d-501">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="50e5d-502">Выполняются после авторизации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-502">Run after authorization.</span></span>  
  * <span data-ttu-id="50e5d-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> может выполнять код до остальной части конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="50e5d-504">Например, `OnResourceExecuting` может выполнять код до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="50e5d-504">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="50e5d-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> может выполнять код после завершения остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="50e5d-506">[Фильтры действий](#action-filters) могут выполнять код непосредственно до и после вызова отдельного метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-506">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="50e5d-507">С их помощью можно управлять аргументами, передаваемыми в действие, и возвращаемым из него результатом.</span><span class="sxs-lookup"><span data-stu-id="50e5d-507">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="50e5d-508">Фильтры действий **не** поддерживаются на Razor страницах.</span><span class="sxs-lookup"><span data-stu-id="50e5d-508">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="50e5d-509">[Фильтры исключений](#exception-filters) служат для применения глобальных политик к необработанным исключениям, которые происходят до записи данных в тело ответа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-509">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="50e5d-510">[Фильтры результатов](#result-filters) могут выполнять код непосредственно до и после выполнения результатов отдельного действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-510">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="50e5d-511">Они выполняются только в том случае, если метод действия выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="50e5d-511">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="50e5d-512">Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="50e5d-512">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="50e5d-513">На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-513">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="50e5d-516">Реализация</span><span class="sxs-lookup"><span data-stu-id="50e5d-516">Implementation</span></span>

<span data-ttu-id="50e5d-517">Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-517">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="50e5d-518">Синхронные фильтры могут выполнять код до (`On-Stage-Executing`) и после (`On-Stage-Executed`) этапа конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-518">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="50e5d-519">Например, `OnActionExecuting` вызывается перед вызовом метода действия,</span><span class="sxs-lookup"><span data-stu-id="50e5d-519">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="50e5d-520">а `OnActionExecuted` — после возврата метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-520">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="50e5d-521">Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-521">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="50e5d-522">В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-522">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="50e5d-523">Каждый из методов `On-Stage-ExecutionAsync` принимает `FilterType-ExecutionDelegate` для выполнения этапа конвейера фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-523">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="50e5d-524">Несколько этапов фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-524">Multiple filter stages</span></span>

<span data-ttu-id="50e5d-525">Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе.</span><span class="sxs-lookup"><span data-stu-id="50e5d-525">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="50e5d-526">Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует интерфейсы `IActionFilter` и `IResultFilter`, а также их асинхронные эквиваленты.</span><span class="sxs-lookup"><span data-stu-id="50e5d-526">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="50e5d-527">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="50e5d-527">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="50e5d-528">Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он.</span><span class="sxs-lookup"><span data-stu-id="50e5d-528">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="50e5d-529">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-529">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="50e5d-530">Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод.</span><span class="sxs-lookup"><span data-stu-id="50e5d-530">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="50e5d-531">При использовании абстрактных классов, таких как <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, следует переопределять только синхронные методы или асинхронный метод каждого типа фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-531">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="50e5d-532">Встроенные атрибуты фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-532">Built-in filter attributes</span></span>

<span data-ttu-id="50e5d-533">ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="50e5d-533">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="50e5d-534">Например, следующий фильтр результатов добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="50e5d-534">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="50e5d-535">Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше.</span><span class="sxs-lookup"><span data-stu-id="50e5d-535">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="50e5d-536">Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:</span><span class="sxs-lookup"><span data-stu-id="50e5d-536">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="50e5d-537">Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.</span><span class="sxs-lookup"><span data-stu-id="50e5d-537">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="50e5d-538">Атрибуты фильтров:</span><span class="sxs-lookup"><span data-stu-id="50e5d-538">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="50e5d-539">Области и порядок выполнения фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-539">Filter scopes and order of execution</span></span>

<span data-ttu-id="50e5d-540">Фильтр можно добавить в конвейер в одной из трех *областей*:</span><span class="sxs-lookup"><span data-stu-id="50e5d-540">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="50e5d-541">С помощью атрибута в действии.</span><span class="sxs-lookup"><span data-stu-id="50e5d-541">Using an attribute on an action.</span></span>
* <span data-ttu-id="50e5d-542">С помощью атрибута в контроллере.</span><span class="sxs-lookup"><span data-stu-id="50e5d-542">Using an attribute on a controller.</span></span>
* <span data-ttu-id="50e5d-543">Глобально для всех контроллеров и действий, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="50e5d-543">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="50e5d-544">Предыдущий код глобально добавляет три фильтра с помощью коллекции [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="50e5d-544">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="50e5d-545">Порядок выполнения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="50e5d-545">Default order of execution</span></span>

<span data-ttu-id="50e5d-546">Если есть несколько *одинаковых* фильтров, область определяет порядок их выполнения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="50e5d-546">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="50e5d-547">Глобальные фильтры заключают в себя фильтры классов,</span><span class="sxs-lookup"><span data-stu-id="50e5d-547">Global filters surround class filters.</span></span> <span data-ttu-id="50e5d-548">которые, в свою очередь, заключают в себя фильтры методов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-548">Class filters surround method filters.</span></span>

<span data-ttu-id="50e5d-549">В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода.</span><span class="sxs-lookup"><span data-stu-id="50e5d-549">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="50e5d-550">Последовательность фильтров:</span><span class="sxs-lookup"><span data-stu-id="50e5d-550">The filter sequence:</span></span>

* <span data-ttu-id="50e5d-551">*Предшествующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-551">The *before* code of global filters.</span></span>
  * <span data-ttu-id="50e5d-552">*Предшествующий* код фильтров контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-552">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="50e5d-553">*Предшествующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-553">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="50e5d-554">*Последующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-554">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="50e5d-555">*Последующий* код фильтров контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-555">The *after* code of controller filters.</span></span>
* <span data-ttu-id="50e5d-556">*Последующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-556">The *after* code of global filters.</span></span>
  
<span data-ttu-id="50e5d-557">В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-557">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="50e5d-558">Последовательность</span><span class="sxs-lookup"><span data-stu-id="50e5d-558">Sequence</span></span> | <span data-ttu-id="50e5d-559">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-559">Filter scope</span></span> | <span data-ttu-id="50e5d-560">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-560">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="50e5d-561">1</span><span class="sxs-lookup"><span data-stu-id="50e5d-561">1</span></span> | <span data-ttu-id="50e5d-562">Глобальный</span><span class="sxs-lookup"><span data-stu-id="50e5d-562">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="50e5d-563">2</span><span class="sxs-lookup"><span data-stu-id="50e5d-563">2</span></span> | <span data-ttu-id="50e5d-564">Контроллер</span><span class="sxs-lookup"><span data-stu-id="50e5d-564">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="50e5d-565">3</span><span class="sxs-lookup"><span data-stu-id="50e5d-565">3</span></span> | <span data-ttu-id="50e5d-566">Метод</span><span class="sxs-lookup"><span data-stu-id="50e5d-566">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="50e5d-567">4</span><span class="sxs-lookup"><span data-stu-id="50e5d-567">4</span></span> | <span data-ttu-id="50e5d-568">Метод</span><span class="sxs-lookup"><span data-stu-id="50e5d-568">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="50e5d-569">5</span><span class="sxs-lookup"><span data-stu-id="50e5d-569">5</span></span> | <span data-ttu-id="50e5d-570">Контроллер</span><span class="sxs-lookup"><span data-stu-id="50e5d-570">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="50e5d-571">6</span><span class="sxs-lookup"><span data-stu-id="50e5d-571">6</span></span> | <span data-ttu-id="50e5d-572">Глобальный</span><span class="sxs-lookup"><span data-stu-id="50e5d-572">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="50e5d-573">Эта последовательность показывает:</span><span class="sxs-lookup"><span data-stu-id="50e5d-573">This sequence shows:</span></span>

* <span data-ttu-id="50e5d-574">Фильтр метода вкладывается в фильтр контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-574">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="50e5d-575">Фильтр контроллера вкладывается в глобальный фильтр.</span><span class="sxs-lookup"><span data-stu-id="50e5d-575">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="50e5d-576">RazorФильтры уровня контроллера и страницы</span><span class="sxs-lookup"><span data-stu-id="50e5d-576">Controller and Razor Page level filters</span></span>

<span data-ttu-id="50e5d-577">Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-577">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="50e5d-578">Эти методы:</span><span class="sxs-lookup"><span data-stu-id="50e5d-578">These methods:</span></span>

* <span data-ttu-id="50e5d-579">Заключают фильтры, которые выполняются для указанного действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-579">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="50e5d-580">`OnActionExecuting` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-580">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="50e5d-581">`OnActionExecuted` вызывается после всех фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-581">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="50e5d-582">`OnActionExecutionAsync` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-582">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="50e5d-583">Код в фильтре после `next` выполняется после метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-583">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="50e5d-584">Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.</span><span class="sxs-lookup"><span data-stu-id="50e5d-584">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="50e5d-585">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-585">The `TestController`:</span></span>

* <span data-ttu-id="50e5d-586">Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-586">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="50e5d-587">Переопределяет `OnActionExecuting` и `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-587">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="50e5d-588">Переходит к `https://localhost:5001/Test/FilterTest2` для выполнения следующего кода:</span><span class="sxs-lookup"><span data-stu-id="50e5d-588">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="50e5d-589">Для Razor страниц см. раздел [реализация Razor фильтров страниц путем переопределения методов фильтров](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="50e5d-589">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="50e5d-590">Переопределение порядка по умолчанию</span><span class="sxs-lookup"><span data-stu-id="50e5d-590">Overriding the default order</span></span>

<span data-ttu-id="50e5d-591">Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-591">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="50e5d-592">`IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения.</span><span class="sxs-lookup"><span data-stu-id="50e5d-592">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="50e5d-593">Фильтр со значением меньше `Order`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-593">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="50e5d-594">Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-594">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="50e5d-595">Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-595">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="50e5d-596">Свойство `Order` можно задать с помощью параметра конструктора:</span><span class="sxs-lookup"><span data-stu-id="50e5d-596">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="50e5d-597">Рассмотрим три фильтра действий, показанные в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="50e5d-597">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="50e5d-598">Если свойство `Order` контроллера и глобальные фильтры имеют значения 1 и 2 соответственно, порядок выполнения инвертируется.</span><span class="sxs-lookup"><span data-stu-id="50e5d-598">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="50e5d-599">Последовательность</span><span class="sxs-lookup"><span data-stu-id="50e5d-599">Sequence</span></span> | <span data-ttu-id="50e5d-600">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-600">Filter scope</span></span> | <span data-ttu-id="50e5d-601">Свойство`Order`</span><span class="sxs-lookup"><span data-stu-id="50e5d-601">`Order` property</span></span> | <span data-ttu-id="50e5d-602">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="50e5d-602">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="50e5d-603">1</span><span class="sxs-lookup"><span data-stu-id="50e5d-603">1</span></span> | <span data-ttu-id="50e5d-604">Метод</span><span class="sxs-lookup"><span data-stu-id="50e5d-604">Method</span></span> | <span data-ttu-id="50e5d-605">0</span><span class="sxs-lookup"><span data-stu-id="50e5d-605">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="50e5d-606">2</span><span class="sxs-lookup"><span data-stu-id="50e5d-606">2</span></span> | <span data-ttu-id="50e5d-607">Контроллер</span><span class="sxs-lookup"><span data-stu-id="50e5d-607">Controller</span></span> | <span data-ttu-id="50e5d-608">1</span><span class="sxs-lookup"><span data-stu-id="50e5d-608">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="50e5d-609">3</span><span class="sxs-lookup"><span data-stu-id="50e5d-609">3</span></span> | <span data-ttu-id="50e5d-610">Глобальный</span><span class="sxs-lookup"><span data-stu-id="50e5d-610">Global</span></span> | <span data-ttu-id="50e5d-611">2</span><span class="sxs-lookup"><span data-stu-id="50e5d-611">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="50e5d-612">4</span><span class="sxs-lookup"><span data-stu-id="50e5d-612">4</span></span> | <span data-ttu-id="50e5d-613">Глобальный</span><span class="sxs-lookup"><span data-stu-id="50e5d-613">Global</span></span> | <span data-ttu-id="50e5d-614">2</span><span class="sxs-lookup"><span data-stu-id="50e5d-614">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="50e5d-615">5</span><span class="sxs-lookup"><span data-stu-id="50e5d-615">5</span></span> | <span data-ttu-id="50e5d-616">Контроллер</span><span class="sxs-lookup"><span data-stu-id="50e5d-616">Controller</span></span> | <span data-ttu-id="50e5d-617">1</span><span class="sxs-lookup"><span data-stu-id="50e5d-617">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="50e5d-618">6</span><span class="sxs-lookup"><span data-stu-id="50e5d-618">6</span></span> | <span data-ttu-id="50e5d-619">Метод</span><span class="sxs-lookup"><span data-stu-id="50e5d-619">Method</span></span> | <span data-ttu-id="50e5d-620">0</span><span class="sxs-lookup"><span data-stu-id="50e5d-620">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="50e5d-621">Свойство `Order` переопределяет область при определении порядка выполнения фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-621">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="50e5d-622">Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области.</span><span class="sxs-lookup"><span data-stu-id="50e5d-622">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="50e5d-623">Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0.</span><span class="sxs-lookup"><span data-stu-id="50e5d-623">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="50e5d-624">Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-624">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="50e5d-625">Отмена и сокращенное выполнение</span><span class="sxs-lookup"><span data-stu-id="50e5d-625">Cancellation and short-circuiting</span></span>

<span data-ttu-id="50e5d-626">Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-626">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="50e5d-627">Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:</span><span class="sxs-lookup"><span data-stu-id="50e5d-627">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="50e5d-628">В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-628">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="50e5d-629">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-629">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="50e5d-630">Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-630">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="50e5d-631">Замыкает оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-631">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="50e5d-632">Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-632">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="50e5d-633">Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="50e5d-633">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="50e5d-634">Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-634">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="50e5d-635">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="50e5d-635">Dependency injection</span></span>

<span data-ttu-id="50e5d-636">Фильтры можно добавлять по типу или экземпляру.</span><span class="sxs-lookup"><span data-stu-id="50e5d-636">Filters can be added by type or by instance.</span></span> <span data-ttu-id="50e5d-637">Добавляемый экземпляр используется для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-637">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="50e5d-638">Если добавляется тип, он является активированным.</span><span class="sxs-lookup"><span data-stu-id="50e5d-638">If a type is added, it's type-activated.</span></span> <span data-ttu-id="50e5d-639">Активация фильтра означает, что:</span><span class="sxs-lookup"><span data-stu-id="50e5d-639">A type-activated filter means:</span></span>

* <span data-ttu-id="50e5d-640">Экземпляр создается для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="50e5d-640">An instance is created for each request.</span></span>
* <span data-ttu-id="50e5d-641">Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="50e5d-641">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="50e5d-642">Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="50e5d-642">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="50e5d-643">Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:</span><span class="sxs-lookup"><span data-stu-id="50e5d-643">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="50e5d-644">Параметры конструктора должны предоставляться для атрибутов в месте их применения.</span><span class="sxs-lookup"><span data-stu-id="50e5d-644">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="50e5d-645">Это ограничение, налагаемое на использование атрибутов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-645">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="50e5d-646">Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="50e5d-646">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="50e5d-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="50e5d-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="50e5d-648">Предыдущие фильтры могут применяться к контроллеру или методу действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-648">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="50e5d-649">Средства ведения журнала доступны путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-649">Loggers are available from DI.</span></span> <span data-ttu-id="50e5d-650">Но следует избегать создания и использования фильтров исключительно для целей ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="50e5d-650">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="50e5d-651">[Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="50e5d-651">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="50e5d-652">При добавлении ведения журналов в фильтры следует учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="50e5d-652">Logging added to filters:</span></span>

* <span data-ttu-id="50e5d-653">Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.</span><span class="sxs-lookup"><span data-stu-id="50e5d-653">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="50e5d-654">**Не** следует регистрировать действия или другие события платформы,</span><span class="sxs-lookup"><span data-stu-id="50e5d-654">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="50e5d-655">так как это делают встроенные фильтры.</span><span class="sxs-lookup"><span data-stu-id="50e5d-655">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="50e5d-656">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="50e5d-656">ServiceFilterAttribute</span></span>

<span data-ttu-id="50e5d-657">Типы реализации фильтра службы регистрируются в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-657">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="50e5d-658">Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-658">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="50e5d-659">В следующем коде используется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-659">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="50e5d-660">В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-660">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="50e5d-661">В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="50e5d-661">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="50e5d-662">При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="50e5d-662">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="50e5d-663">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="50e5d-663">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="50e5d-664">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="50e5d-664">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="50e5d-665">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="50e5d-665">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="50e5d-666">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="50e5d-666">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="50e5d-667">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="50e5d-667">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="50e5d-668">Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="50e5d-669">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-669">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="50e5d-670">`CreateInstance` загружает указанный тип из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-670">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="50e5d-671">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="50e5d-671">TypeFilterAttribute</span></span>

<span data-ttu-id="50e5d-672">Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="50e5d-673">Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-673">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="50e5d-674">Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="50e5d-674">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="50e5d-675">Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-675">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="50e5d-676">Их зависимости выполняются самим контейнером.</span><span class="sxs-lookup"><span data-stu-id="50e5d-676">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="50e5d-677">Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-677">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="50e5d-678">При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="50e5d-678">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="50e5d-679">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="50e5d-679">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="50e5d-680">Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-680">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="50e5d-681">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="50e5d-681">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="50e5d-682">В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-682">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="50e5d-683">Фильтры авторизации</span><span class="sxs-lookup"><span data-stu-id="50e5d-683">Authorization filters</span></span>

<span data-ttu-id="50e5d-684">Фильтры авторизации:</span><span class="sxs-lookup"><span data-stu-id="50e5d-684">Authorization filters:</span></span>

* <span data-ttu-id="50e5d-685">Выполняются в первую очередь в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-685">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="50e5d-686">Контролируют доступ к методам действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-686">Control access to action methods.</span></span>
* <span data-ttu-id="50e5d-687">Имеют предшествующий, но не последующий метод.</span><span class="sxs-lookup"><span data-stu-id="50e5d-687">Have a before method, but no after method.</span></span>

<span data-ttu-id="50e5d-688">Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-688">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="50e5d-689">Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра.</span><span class="sxs-lookup"><span data-stu-id="50e5d-689">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="50e5d-690">Встроенный фильтр авторизации:</span><span class="sxs-lookup"><span data-stu-id="50e5d-690">The built-in authorization filter:</span></span>

* <span data-ttu-id="50e5d-691">Вызывает систему авторизации.</span><span class="sxs-lookup"><span data-stu-id="50e5d-691">Calls the authorization system.</span></span>
* <span data-ttu-id="50e5d-692">Не выполняет авторизацию запросов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-692">Does not authorize requests.</span></span>

<span data-ttu-id="50e5d-693">**Не** вызывайте исключения в фильтрах авторизации:</span><span class="sxs-lookup"><span data-stu-id="50e5d-693">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="50e5d-694">Исключение не будет обработано.</span><span class="sxs-lookup"><span data-stu-id="50e5d-694">The exception will not be handled.</span></span>
* <span data-ttu-id="50e5d-695">Фильтры исключений не будут обрабатывать исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-695">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="50e5d-696">При возникновении исключения в фильтре авторизации попробуйте создать запрос.</span><span class="sxs-lookup"><span data-stu-id="50e5d-696">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="50e5d-697">Дополнительные сведения об [авторизации](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="50e5d-697">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="50e5d-698">Фильтры ресурсов</span><span class="sxs-lookup"><span data-stu-id="50e5d-698">Resource filters</span></span>

<span data-ttu-id="50e5d-699">Фильтры ресурсов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-699">Resource filters:</span></span>

* <span data-ttu-id="50e5d-700">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="50e5d-701">Выполнение охватывает большую часть конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-701">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="50e5d-702">До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).</span><span class="sxs-lookup"><span data-stu-id="50e5d-702">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="50e5d-703">Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-703">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="50e5d-704">Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.</span><span class="sxs-lookup"><span data-stu-id="50e5d-704">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="50e5d-705">Примеры фильтров ресурсов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-705">Resource filter examples:</span></span>

* <span data-ttu-id="50e5d-706">[Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.</span><span class="sxs-lookup"><span data-stu-id="50e5d-706">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="50e5d-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="50e5d-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="50e5d-708">Предотвращает доступ привязки модели к данным формы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-708">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="50e5d-709">Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.</span><span class="sxs-lookup"><span data-stu-id="50e5d-709">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="50e5d-710">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="50e5d-710">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="50e5d-711">Фильтры действий **не** применяются к Razor страницам.</span><span class="sxs-lookup"><span data-stu-id="50e5d-711">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="50e5d-712">Razor Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="50e5d-712">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="50e5d-713">Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="50e5d-713">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="50e5d-714">Фильтры действий:</span><span class="sxs-lookup"><span data-stu-id="50e5d-714">Action filters:</span></span>

* <span data-ttu-id="50e5d-715">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-715">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="50e5d-716">Их выполнение охватывает выполнение методов действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-716">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="50e5d-717">В следующем фрагменте кода показан пример фильтра действий:</span><span class="sxs-lookup"><span data-stu-id="50e5d-717">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="50e5d-718"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="50e5d-718">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="50e5d-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="50e5d-720"><xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="50e5d-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="50e5d-722">Вызов исключения в методе действия:</span><span class="sxs-lookup"><span data-stu-id="50e5d-722">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="50e5d-723">Предотвращает выполнение последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-723">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="50e5d-724">В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="50e5d-724">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="50e5d-725"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="50e5d-725">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="50e5d-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="50e5d-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="50e5d-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="50e5d-728">При присвоении этому свойству ненулевого значения:</span><span class="sxs-lookup"><span data-stu-id="50e5d-728">Setting this property to null:</span></span>

  * <span data-ttu-id="50e5d-729">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-729">Effectively handles the exception.</span></span>
  * <span data-ttu-id="50e5d-730">`Result` будет выполняться так, как при возвращении методом действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-730">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="50e5d-731">Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="50e5d-731">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="50e5d-732">Приводит к выполнению последующих фильтров действий и метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-732">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="50e5d-733">Возвращает `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-733">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="50e5d-734">Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="50e5d-734">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="50e5d-735">Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-735">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="50e5d-736">Фильтр действий `OnActionExecuting` можно использовать:</span><span class="sxs-lookup"><span data-stu-id="50e5d-736">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="50e5d-737">Для проверки состояния модели.</span><span class="sxs-lookup"><span data-stu-id="50e5d-737">Validate model state.</span></span>
* <span data-ttu-id="50e5d-738">Для получения ошибки, если состояние является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="50e5d-738">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="50e5d-739">Метод `OnActionExecuted` выполняется после метода действия:</span><span class="sxs-lookup"><span data-stu-id="50e5d-739">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="50e5d-740">Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-740">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="50e5d-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="50e5d-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="50e5d-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="50e5d-743">Если установить для `Exception` значение NULL:</span><span class="sxs-lookup"><span data-stu-id="50e5d-743">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="50e5d-744">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-744">Effectively handles an exception.</span></span>
  * <span data-ttu-id="50e5d-745">`ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.</span><span class="sxs-lookup"><span data-stu-id="50e5d-745">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="50e5d-746">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="50e5d-746">Exception filters</span></span>

<span data-ttu-id="50e5d-747">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-747">Exception filters:</span></span>

* <span data-ttu-id="50e5d-748">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-748">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="50e5d-749">Можно использовать для реализации политик обработки стандартных ошибок.</span><span class="sxs-lookup"><span data-stu-id="50e5d-749">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="50e5d-750">В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:</span><span class="sxs-lookup"><span data-stu-id="50e5d-750">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="50e5d-751">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-751">Exception filters:</span></span>

* <span data-ttu-id="50e5d-752">Не имеют предшествующих и последующих событий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-752">Don't have before and after events.</span></span>
* <span data-ttu-id="50e5d-753">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-753">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="50e5d-754">Обрабатывать необработанные исключения, происходящие при Razor создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-754">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="50e5d-755">**Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.</span><span class="sxs-lookup"><span data-stu-id="50e5d-755">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="50e5d-756">Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ.</span><span class="sxs-lookup"><span data-stu-id="50e5d-756">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="50e5d-757">Это предотвратит распространение исключения.</span><span class="sxs-lookup"><span data-stu-id="50e5d-757">This stops propagation of the exception.</span></span> <span data-ttu-id="50e5d-758">Фильтр исключений не может преобразовать исключение в успешное выполнение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-758">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="50e5d-759">Это может сделать только фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-759">Only an action filter can do that.</span></span>

<span data-ttu-id="50e5d-760">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="50e5d-760">Exception filters:</span></span>

* <span data-ttu-id="50e5d-761">Хорошо подходят для перехвата исключений, возникающих в действиях.</span><span class="sxs-lookup"><span data-stu-id="50e5d-761">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="50e5d-762">Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="50e5d-762">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="50e5d-763">ПО промежуточного слоя хорошо подходит для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="50e5d-763">Prefer middleware for exception handling.</span></span> <span data-ttu-id="50e5d-764">Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-764">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="50e5d-765">Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML.</span><span class="sxs-lookup"><span data-stu-id="50e5d-765">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="50e5d-766">Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="50e5d-766">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="50e5d-767">Фильтры результатов</span><span class="sxs-lookup"><span data-stu-id="50e5d-767">Result filters</span></span>

<span data-ttu-id="50e5d-768">Фильтры результатов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-768">Result filters:</span></span>

* <span data-ttu-id="50e5d-769">Реализация интерфейса:</span><span class="sxs-lookup"><span data-stu-id="50e5d-769">Implement an interface:</span></span>
  * <span data-ttu-id="50e5d-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="50e5d-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="50e5d-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="50e5d-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="50e5d-772">Их выполнение охватывает выполнение результатов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-772">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="50e5d-773">IResultFilter и IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="50e5d-773">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="50e5d-774">В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:</span><span class="sxs-lookup"><span data-stu-id="50e5d-774">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="50e5d-775">Тип выполняемого результата зависит от соответствующего действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-775">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="50e5d-776">Действие, возвращающее представление, будет включать всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-776">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="50e5d-777">В процессе выполнения результата метод API может производить сериализацию.</span><span class="sxs-lookup"><span data-stu-id="50e5d-777">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="50e5d-778">Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="50e5d-778">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="50e5d-779">Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-779">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="50e5d-780">Фильтры результатов не выполняются в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="50e5d-780">Result filters are not executed when:</span></span>

* <span data-ttu-id="50e5d-781">Фильтр авторизации или ресурсов выполняет сокращение конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-781">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="50e5d-782">Фильтр исключений обрабатывает исключение и выдает результат действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-782">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="50e5d-783">Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-783">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="50e5d-784">При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа.</span><span class="sxs-lookup"><span data-stu-id="50e5d-784">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="50e5d-785">Вызов исключения в `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-785">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="50e5d-786">Предотвращает выполнение результата действия и последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-786">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="50e5d-787">Рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="50e5d-787">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="50e5d-788">Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту.</span><span class="sxs-lookup"><span data-stu-id="50e5d-788">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="50e5d-789">Если ответ уже был отправлен клиенту его больше нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="50e5d-789">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="50e5d-790">`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="50e5d-790">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="50e5d-791">`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="50e5d-791">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="50e5d-792">Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов ASP.NET Core на дальнейших этапах конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-792">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="50e5d-793">Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует.</span><span class="sxs-lookup"><span data-stu-id="50e5d-793">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="50e5d-794">Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.</span><span class="sxs-lookup"><span data-stu-id="50e5d-794">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="50e5d-795">Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия.</span><span class="sxs-lookup"><span data-stu-id="50e5d-795">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="50e5d-796">Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-796">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="50e5d-797">Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="50e5d-797">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="50e5d-798">Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.</span><span class="sxs-lookup"><span data-stu-id="50e5d-798">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="50e5d-799">IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="50e5d-799">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="50e5d-800">Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий.</span><span class="sxs-lookup"><span data-stu-id="50e5d-800">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="50e5d-801">Сюда включены результаты действий, созданные:</span><span class="sxs-lookup"><span data-stu-id="50e5d-801">This includes action results produced by:</span></span>

* <span data-ttu-id="50e5d-802">фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;</span><span class="sxs-lookup"><span data-stu-id="50e5d-802">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="50e5d-803">фильтрами исключений.</span><span class="sxs-lookup"><span data-stu-id="50e5d-803">Exception filters.</span></span>

<span data-ttu-id="50e5d-804">Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:</span><span class="sxs-lookup"><span data-stu-id="50e5d-804">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

## <a name="ifilterfactory"></a><span data-ttu-id="50e5d-805">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="50e5d-805">IFilterFactory</span></span>

<span data-ttu-id="50e5d-806">Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="50e5d-807">Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-807">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="50e5d-808">Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-808">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="50e5d-809">Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-809">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="50e5d-810">Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-810">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="50e5d-811">Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:</span><span class="sxs-lookup"><span data-stu-id="50e5d-811">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="50e5d-812">Приведенный выше код можно проверить, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="50e5d-812">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="50e5d-813">Вызовите средства для разработчика (F12).</span><span class="sxs-lookup"><span data-stu-id="50e5d-813">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="50e5d-814">Перейдите к `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-814">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="50e5d-815">В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:</span><span class="sxs-lookup"><span data-stu-id="50e5d-815">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="50e5d-816">**Автор:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="50e5d-816">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="50e5d-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="50e5d-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="50e5d-818">**внутренний:**`My header`</span><span class="sxs-lookup"><span data-stu-id="50e5d-818">**internal:** `My header`</span></span>

<span data-ttu-id="50e5d-819">Приведенный выше код создает заголовок ответа **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-819">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="50e5d-820">Реализация IFilterFactory в атрибуте</span><span class="sxs-lookup"><span data-stu-id="50e5d-820">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="50e5d-821">Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:</span><span class="sxs-lookup"><span data-stu-id="50e5d-821">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="50e5d-822">Не требуют передачи параметров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-822">Don't require passing parameters.</span></span>
* <span data-ttu-id="50e5d-823">Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="50e5d-823">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="50e5d-824">Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="50e5d-825">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="50e5d-825">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="50e5d-826">`CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).</span><span class="sxs-lookup"><span data-stu-id="50e5d-826">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="50e5d-827">В коде ниже приведено три примера применения `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="50e5d-827">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="50e5d-828">В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="50e5d-828">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="50e5d-829">Использование ПО промежуточного слоя в конвейере фильтров</span><span class="sxs-lookup"><span data-stu-id="50e5d-829">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="50e5d-830">Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере.</span><span class="sxs-lookup"><span data-stu-id="50e5d-830">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="50e5d-831">При этом фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения ASP.NET Core, то есть они имеют доступ к контексту и конструкциям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50e5d-831">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="50e5d-832">Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров.</span><span class="sxs-lookup"><span data-stu-id="50e5d-832">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="50e5d-833">В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:</span><span class="sxs-lookup"><span data-stu-id="50e5d-833">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="50e5d-834">Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="50e5d-834">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="50e5d-835">Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="50e5d-835">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="50e5d-836">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="50e5d-836">Next actions</span></span>

* <span data-ttu-id="50e5d-837">См. раздел [методы фильтров для Razor страниц](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="50e5d-837">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="50e5d-838">Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="50e5d-838">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
