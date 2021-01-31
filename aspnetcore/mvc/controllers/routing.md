---
title: Маршрутизация к действиям контроллера в ASP.NET Core
author: rick-anderson
description: Узнайте, как в MVC ASP.NET Core используется ПО промежуточного слоя маршрутизации для сопоставления URL-адресов входящих запросов с действиями.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: 0863b5758f33b720636f3b927fcb9014cd106c21
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217548"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="17c28-103">Маршрутизация к действиям контроллера в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17c28-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="17c28-104">Авторы: [Райан Новак](https://github.com/rynowak) (Ryan Nowak), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="17c28-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17c28-105">Контроллеры ASP.NET Core используют по [промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов и сопоставления их с [действиями](#action).</span><span class="sxs-lookup"><span data-stu-id="17c28-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="17c28-106">Шаблоны маршрутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-106">Route templates:</span></span>

* <span data-ttu-id="17c28-107">Определяются в коде запуска или атрибутах.</span><span class="sxs-lookup"><span data-stu-id="17c28-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="17c28-108">Описание способов сопоставления путей URL-адресов с [действиями](#action).</span><span class="sxs-lookup"><span data-stu-id="17c28-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="17c28-109">Используются для создания URL-адресов для ссылок.</span><span class="sxs-lookup"><span data-stu-id="17c28-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="17c28-110">Созданные ссылки обычно возвращаются в ответах.</span><span class="sxs-lookup"><span data-stu-id="17c28-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="17c28-111">Действия либо [направляются по соглашению](#cr) , либо [маршрутизируются по атрибуту](#ar).</span><span class="sxs-lookup"><span data-stu-id="17c28-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="17c28-112">При размещении маршрута на контроллере или [действии](#action) он пересылается по атрибуту.</span><span class="sxs-lookup"><span data-stu-id="17c28-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="17c28-113">Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="17c28-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="17c28-114">Этот документ:</span><span class="sxs-lookup"><span data-stu-id="17c28-114">This document:</span></span>

* <span data-ttu-id="17c28-115">Объясняет взаимодействие между MVC и маршрутизацией.</span><span class="sxs-lookup"><span data-stu-id="17c28-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="17c28-116">Как обычные приложения MVC используют функции маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="17c28-117">Рассматриваются оба:</span><span class="sxs-lookup"><span data-stu-id="17c28-117">Covers both:</span></span>
    * <span data-ttu-id="17c28-118">[Обычная маршрутизация](#cr) обычно используется с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="17c28-118">[Conventional routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="17c28-119">*Маршрутизация атрибутов* , используемая с API-интерфейсами RESTful.</span><span class="sxs-lookup"><span data-stu-id="17c28-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="17c28-120">Если вы в основном заинтересованы в маршрутизации для API-интерфейсов RESTFUL, перейдите к разделу [Маршрутизация атрибутов для интерфейсов API для остальных](#ar) компонентов.</span><span class="sxs-lookup"><span data-stu-id="17c28-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="17c28-121">Дополнительные сведения о маршрутизации см. в разделе [Маршрутизация](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="17c28-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="17c28-122">Относится к системе маршрутизации по умолчанию, добавленной в ASP.NET Core 3,0, называемую маршрутизацией конечных точек.</span><span class="sxs-lookup"><span data-stu-id="17c28-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="17c28-123">В целях совместимости можно использовать контроллеры с предыдущей версией маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="17c28-124">Инструкции см. в [руководству по миграции 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="17c28-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="17c28-125">Справочные материалы по устаревшей системе маршрутизации см. в [версии 2,2 этого документа](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="17c28-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="17c28-126">Настройка обычного маршрута</span><span class="sxs-lookup"><span data-stu-id="17c28-126">Set up conventional route</span></span>

<span data-ttu-id="17c28-127">`Startup.Configure` При использовании [обычной маршрутизации](#crd)обычно имеет код, аналогичный приведенному ниже.</span><span class="sxs-lookup"><span data-stu-id="17c28-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="17c28-128">Внутри вызова метод <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> используется для создания одного маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="17c28-129">Единственный маршрут называется `default` Route.</span><span class="sxs-lookup"><span data-stu-id="17c28-129">The single route is named `default` route.</span></span> <span data-ttu-id="17c28-130">Большинство приложений с контроллерами и представлениями используют шаблон маршрута, аналогичный `default` маршруту.</span><span class="sxs-lookup"><span data-stu-id="17c28-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="17c28-131">Интерфейсы API-интерфейсов для интерфейса остальных должны использовать [маршрутизацию атрибутов](#ar).</span><span class="sxs-lookup"><span data-stu-id="17c28-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="17c28-132">Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="17c28-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="17c28-133">Соответствует URL-пути, например `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="17c28-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="17c28-134">Извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем маркировки пути.</span><span class="sxs-lookup"><span data-stu-id="17c28-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="17c28-135">Извлечение значений маршрута приводит к совпадению, если у приложения есть контроллер с именем `ProductsController` и `Details` действие:</span><span class="sxs-lookup"><span data-stu-id="17c28-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="17c28-136">`/Products/Details/5` модель привязывает значение, `id = 5` чтобы задать `id` параметр `5` .</span><span class="sxs-lookup"><span data-stu-id="17c28-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="17c28-137">Дополнительные сведения см. в разделе [Привязка модели](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="17c28-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="17c28-138">`{controller=Home}` Определяет `Home` как значение по умолчанию `controller` .</span><span class="sxs-lookup"><span data-stu-id="17c28-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="17c28-139">`{action=Index}` Определяет `Index` как значение по умолчанию `action` .</span><span class="sxs-lookup"><span data-stu-id="17c28-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="17c28-140">`?`Символ в `{id?}` определяет `id` как необязательный.</span><span class="sxs-lookup"><span data-stu-id="17c28-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="17c28-141">Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="17c28-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="17c28-142">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="17c28-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="17c28-143">Соответствует пути URL-адреса `/` .</span><span class="sxs-lookup"><span data-stu-id="17c28-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="17c28-144">Создает значения маршрута `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="17c28-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="17c28-145">Значения для `controller` и `action` используют значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="17c28-146">`id` не создает значение, так как в URL-пути нет соответствующего сегмента.</span><span class="sxs-lookup"><span data-stu-id="17c28-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="17c28-147">`/` соответствует, только если существует `HomeController` действие и `Index` :</span><span class="sxs-lookup"><span data-stu-id="17c28-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="17c28-148">Используя предыдущее определение контроллера и шаблон маршрута, `HomeController.Index` действие выполняется для следующих URL-путей:</span><span class="sxs-lookup"><span data-stu-id="17c28-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="17c28-149">URL-путь `/` использует `Home` контроллеры и действие шаблона маршрута по умолчанию `Index` .</span><span class="sxs-lookup"><span data-stu-id="17c28-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="17c28-150">URL-путь `/Home` использует действие по умолчанию шаблона маршрута `Index` .</span><span class="sxs-lookup"><span data-stu-id="17c28-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="17c28-151">Универсальный метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span><span class="sxs-lookup"><span data-stu-id="17c28-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="17c28-152">Меняющей</span><span class="sxs-lookup"><span data-stu-id="17c28-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="17c28-153">Маршрутизация настраивается с помощью по <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> промежуточного слоя и.</span><span class="sxs-lookup"><span data-stu-id="17c28-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="17c28-154">Использование контроллеров:</span><span class="sxs-lookup"><span data-stu-id="17c28-154">To use controllers:</span></span>
>
> * <span data-ttu-id="17c28-155">Вызовите <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> внутри `UseEndpoints` , чтобы сопоставлять [атрибуты, перенаправляемые](#ar) контроллерам.</span><span class="sxs-lookup"><span data-stu-id="17c28-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="17c28-156">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> или <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> , чтобы связать как [соглашение с маршрутизацией](#cr) , так и [перенаправляемые](#ar) контроллеры.</span><span class="sxs-lookup"><span data-stu-id="17c28-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="17c28-157">Маршрутизация на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="17c28-157">Conventional routing</span></span>

<span data-ttu-id="17c28-158">Стандартная маршрутизация используется с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="17c28-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="17c28-159">Маршрут `default`:</span><span class="sxs-lookup"><span data-stu-id="17c28-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="17c28-160">Предыдущий маршрут является примером *обычного маршрута*.</span><span class="sxs-lookup"><span data-stu-id="17c28-160">The preceding is an example of a *conventional route*.</span></span> <span data-ttu-id="17c28-161">Он называется *обычной маршрутизацией* , так как он устанавливает *соглашение* для URL-путей:</span><span class="sxs-lookup"><span data-stu-id="17c28-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="17c28-162">Первый сегмент пути, `{controller=Home}` сопоставляется с именем контроллера.</span><span class="sxs-lookup"><span data-stu-id="17c28-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="17c28-163">Второй сегмент, `{action=Index}` сопоставляется с именем [действия](#action) .</span><span class="sxs-lookup"><span data-stu-id="17c28-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="17c28-164">Третий сегмент `{id?}` используется для необязательного `id` .</span><span class="sxs-lookup"><span data-stu-id="17c28-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="17c28-165">`?`В `{id?}` делает его необязательным.</span><span class="sxs-lookup"><span data-stu-id="17c28-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="17c28-166">`id` используется для соотнесения с сущностью модели.</span><span class="sxs-lookup"><span data-stu-id="17c28-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="17c28-167">При использовании этого `default` маршрута путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="17c28-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="17c28-168">`/Products/List` сопоставляется с `ProductsController.List` действием.</span><span class="sxs-lookup"><span data-stu-id="17c28-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="17c28-169">`/Blog/Article/17` сопоставляется с `BlogController.Article` и, как правило, модель привязывает `id` параметр к 17.</span><span class="sxs-lookup"><span data-stu-id="17c28-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="17c28-170">Это сопоставление:</span><span class="sxs-lookup"><span data-stu-id="17c28-170">This mapping:</span></span>

* <span data-ttu-id="17c28-171">Основаны **только** на именах контроллера и [действий](#action) .</span><span class="sxs-lookup"><span data-stu-id="17c28-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="17c28-172">Не основано на пространствах имен, расположениях исходных файлов или параметров метода.</span><span class="sxs-lookup"><span data-stu-id="17c28-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="17c28-173">Использование обычной маршрутизации с маршрутом по умолчанию позволяет создавать приложения без необходимости создания нового шаблона URL-адреса для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="17c28-174">Для приложения с действиями в стиле [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) с согласованностью URL-адресов на контроллерах:</span><span class="sxs-lookup"><span data-stu-id="17c28-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="17c28-175">Помогает упростить код.</span><span class="sxs-lookup"><span data-stu-id="17c28-175">Helps simplify the code.</span></span>
* <span data-ttu-id="17c28-176">Делает пользовательский интерфейс более предсказуемым.</span><span class="sxs-lookup"><span data-stu-id="17c28-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="17c28-177">`id`В предыдущем коде шаблон маршрута определен как необязательный.</span><span class="sxs-lookup"><span data-stu-id="17c28-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="17c28-178">Действия могут выполняться без дополнительного идентификатора, предоставленного в качестве части URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="17c28-179">Как правило, если `id` параметр опущен из URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="17c28-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="17c28-180">`id` задается `0` привязкой модели.</span><span class="sxs-lookup"><span data-stu-id="17c28-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="17c28-181">Сущность не найдена в соответствующей базе данных `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="17c28-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="17c28-182">[Маршрутизация атрибутов](#ar) обеспечивает детальный контроль, чтобы сделать идентификатор необходимым для некоторых действий, а не для других.</span><span class="sxs-lookup"><span data-stu-id="17c28-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="17c28-183">По соглашению в документации содержатся необязательные параметры, например, `id` если они, скорее всего, будут отображаться в правильном использовании.</span><span class="sxs-lookup"><span data-stu-id="17c28-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="17c28-184">Для большинства приложений следует выбрать базовую описательную схему маршрутизации таким образом, чтобы URL-адреса были удобочитаемыми и осмысленными.</span><span class="sxs-lookup"><span data-stu-id="17c28-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="17c28-185">Традиционный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="17c28-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="17c28-186">Поддерживает основную и описательную схемы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="17c28-187">Является отправной точкой для приложений на базе пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="17c28-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="17c28-188">— Единственный шаблон маршрута, необходимый для многих приложений пользовательского веб-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="17c28-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="17c28-189">Для больших веб-приложений пользовательского интерфейса часто требуется еще один маршрут, использующий [области](#areas) .</span><span class="sxs-lookup"><span data-stu-id="17c28-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="17c28-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> и <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="17c28-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="17c28-191">Автоматически назначить значение **заказа** своим конечным точкам в соответствии с порядком их вызова.</span><span class="sxs-lookup"><span data-stu-id="17c28-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="17c28-192">Маршрутизация конечных точек в ASP.NET Core 3.0 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="17c28-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="17c28-193">Не имеет концепции маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="17c28-194">Не предоставляет гарантий упорядочения для выполнения расширяемости, все конечные точки обрабатываются одновременно.</span><span class="sxs-lookup"><span data-stu-id="17c28-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="17c28-195">Чтобы увидеть, как встроенные реализации маршрутизации, такие как <xref:Microsoft.AspNetCore.Routing.Route>, сопоставляются с запросами, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="17c28-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="17c28-196">[Маршрутизация атрибутов](#ar) объясняется далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="17c28-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="17c28-197">Несколько обычных маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-197">Multiple conventional routes</span></span>

<span data-ttu-id="17c28-198">В можно добавить несколько [обычных маршрутов](#cr) `UseEndpoints` , добавив дополнительные вызовы в <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="17c28-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="17c28-199">Это позволяет определить несколько соглашений или добавить традиционные маршруты, предназначенные для конкретного [действия](#action), например:</span><span class="sxs-lookup"><span data-stu-id="17c28-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="17c28-200">`blog`Маршрут в приведенном выше коде является **выделенным обычным маршрутом**.</span><span class="sxs-lookup"><span data-stu-id="17c28-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="17c28-201">Он называется выделенным обычным маршрутом по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="17c28-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="17c28-202">В нем используется [Обычная маршрутизация](#cr).</span><span class="sxs-lookup"><span data-stu-id="17c28-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="17c28-203">Он предназначен для конкретного [действия](#action).</span><span class="sxs-lookup"><span data-stu-id="17c28-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="17c28-204">Поскольку `controller` и `action` не отображаются в шаблоне маршрута в `"blog/{*article}"` качестве параметров:</span><span class="sxs-lookup"><span data-stu-id="17c28-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="17c28-205">Они могут иметь только значения по умолчанию `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="17c28-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="17c28-206">Этот маршрут всегда сопоставлен с действием `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="17c28-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="17c28-207">`/Blog`, `/Blog/Article` и `/Blog/{any-string}` — это единственные URL-пути, соответствующие маршруту блога.</span><span class="sxs-lookup"><span data-stu-id="17c28-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="17c28-208">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="17c28-208">The preceding example:</span></span>

* <span data-ttu-id="17c28-209">`blog` маршрут имеет более высокий приоритет для совпадений, чем `default` маршрут, так как он добавляется первым.</span><span class="sxs-lookup"><span data-stu-id="17c28-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="17c28-210">— Пример маршрутизации в [стиле "в формате"](https://developer.mozilla.org/docs/Glossary/Slug) , в которой имя статьи является частью URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="17c28-211">В ASP.NET Core 3,0 и более поздних версиях маршрутизация не выполняет:</span><span class="sxs-lookup"><span data-stu-id="17c28-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="17c28-212">Определите концепцию, называемую *маршрутом*.</span><span class="sxs-lookup"><span data-stu-id="17c28-212">Define a concept called a *route*.</span></span> <span data-ttu-id="17c28-213">`UseRouting` добавляет соответствие маршрута в конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="17c28-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="17c28-214">По `UseRouting` промежуточного слоя просматривает набор конечных точек, определенных в приложении, и выбирает наилучшее совпадение конечных точек на основе запроса.</span><span class="sxs-lookup"><span data-stu-id="17c28-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="17c28-215">Предоставьте гарантии порядка выполнения расширяемости, например <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> или <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="17c28-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="17c28-216">См. раздел о [маршрутизации](xref:fundamentals/routing) для справочных материалов по маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="17c28-217">Стандартный порядок маршрутизации</span><span class="sxs-lookup"><span data-stu-id="17c28-217">Conventional routing order</span></span>

<span data-ttu-id="17c28-218">Обычная маршрутизация соответствует только комбинации действий и контроллера, определенных приложением.</span><span class="sxs-lookup"><span data-stu-id="17c28-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="17c28-219">Это предназначено для упрощения случаев, когда обычные маршруты перекрываются.</span><span class="sxs-lookup"><span data-stu-id="17c28-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="17c28-220">Добавление маршрутов с помощью <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> автоматически назначает значение порядка своим конечным точкам в соответствии с порядком их вызова.</span><span class="sxs-lookup"><span data-stu-id="17c28-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="17c28-221">Совпадает с более высоким приоритетом на маршруте, который отображается ранее.</span><span class="sxs-lookup"><span data-stu-id="17c28-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="17c28-222">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="17c28-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="17c28-223">Как правило, маршруты с областями следует размещать раньше, так как они более специфичны, чем маршруты без области.</span><span class="sxs-lookup"><span data-stu-id="17c28-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="17c28-224">[Выделенные традиционные маршруты](#dcr) с перекрестными параметрами маршрутов `{*article}` , например, могут сделать маршрут слишком [жадным](xref:fundamentals/routing#greedy), то есть сопоставлять URL-адреса, которые вы хотели бы сопоставить с другими маршрутами.</span><span class="sxs-lookup"><span data-stu-id="17c28-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="17c28-225">Помещайте жадные маршруты позже в таблице маршрутов, чтобы предотвратить жадные соответствия.</span><span class="sxs-lookup"><span data-stu-id="17c28-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="17c28-226">Разрешение неоднозначных действий</span><span class="sxs-lookup"><span data-stu-id="17c28-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="17c28-227">При совпадении двух конечных точек через маршрутизацию маршрутизация должна выполнить одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="17c28-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="17c28-228">Выберите лучший кандидат.</span><span class="sxs-lookup"><span data-stu-id="17c28-228">Choose the best candidate.</span></span>
* <span data-ttu-id="17c28-229">Создание исключения.</span><span class="sxs-lookup"><span data-stu-id="17c28-229">Throw an exception.</span></span>

<span data-ttu-id="17c28-230">Например:</span><span class="sxs-lookup"><span data-stu-id="17c28-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="17c28-231">Предыдущий контроллер определяет два соответствующих действия:</span><span class="sxs-lookup"><span data-stu-id="17c28-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="17c28-232">URL-путь `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="17c28-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="17c28-233">Перенаправление данных `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="17c28-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="17c28-234">Это типичный шаблон для контроллеров MVC:</span><span class="sxs-lookup"><span data-stu-id="17c28-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="17c28-235">`Edit(int)` Отображает форму для изменения продукта.</span><span class="sxs-lookup"><span data-stu-id="17c28-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="17c28-236">`Edit(int, Product)` обрабатывает опубликованную форму.</span><span class="sxs-lookup"><span data-stu-id="17c28-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="17c28-237">Чтобы разрешить правильный маршрут, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-237">To resolve the correct route:</span></span>

* <span data-ttu-id="17c28-238">`Edit(int, Product)` выбирается, если запрос является HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="17c28-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="17c28-239">`Edit(int)` выбран, если [команда HTTP](#verb) является любым другим.</span><span class="sxs-lookup"><span data-stu-id="17c28-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="17c28-240">`Edit(int)` обычно вызывается через `GET` .</span><span class="sxs-lookup"><span data-stu-id="17c28-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="17c28-241">Объект <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , предоставляется для маршрутизации, чтобы его можно было выбрать в зависимости от метода HTTP запроса.</span><span class="sxs-lookup"><span data-stu-id="17c28-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="17c28-242">`HttpPostAttribute` `Edit(int, Product)` Чем больше, тем лучше соответствует `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="17c28-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="17c28-243">Важно понимать роль таких атрибутов, как `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="17c28-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="17c28-244">Аналогичные атрибуты определяются для других [HTTP-команд](#verb).</span><span class="sxs-lookup"><span data-stu-id="17c28-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="17c28-245">В [обычной маршрутизации](#cr)для действий используется одно и то же имя действия, когда они входят в форму отображения, Рабочий процесс отправки формы.</span><span class="sxs-lookup"><span data-stu-id="17c28-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="17c28-246">Например, см. раздел [изучение двух методов действия Edit](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="17c28-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="17c28-247">Если службе маршрутизации не удается выбрать лучший вариант, <xref:System.Reflection.AmbiguousMatchException> создается исключение, в котором перечисляются несколько совпадающих конечных точек.</span><span class="sxs-lookup"><span data-stu-id="17c28-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="17c28-248">Имена обычных маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-248">Conventional route names</span></span>

<span data-ttu-id="17c28-249">Строки  `"blog"` и `"default"` в следующих примерах являются обычными именами маршрутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="17c28-250">Имена маршрутов придают маршруту логическое имя.</span><span class="sxs-lookup"><span data-stu-id="17c28-250">The route names give the route a logical name.</span></span> <span data-ttu-id="17c28-251">Именованный маршрут можно использовать для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="17c28-252">Использование именованного маршрута упрощает создание URL-адресов, когда порядок маршрутов может усложнить создание URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="17c28-253">Имена маршрутов должны быть уникальными для всего приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="17c28-254">Имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-254">Route names:</span></span>

* <span data-ttu-id="17c28-255">Не влияют на сопоставление URL-адресов или обработку запросов.</span><span class="sxs-lookup"><span data-stu-id="17c28-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="17c28-256">Используются только для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-256">Are used only for URL generation.</span></span>

<span data-ttu-id="17c28-257">Концепция имени маршрута представлена в маршрутизации как [иендпоинтнамеметадата](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="17c28-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="17c28-258">Термины **имя маршрута** и **имя конечной точки**:</span><span class="sxs-lookup"><span data-stu-id="17c28-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="17c28-259">Являются взаимозаменяемыми.</span><span class="sxs-lookup"><span data-stu-id="17c28-259">Are interchangeable.</span></span>
* <span data-ttu-id="17c28-260">То, какой из них используется в документации и коде, зависит от описываемого API.</span><span class="sxs-lookup"><span data-stu-id="17c28-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="17c28-261">Маршрутизация атрибутов для интерфейсов API RESTFUL</span><span class="sxs-lookup"><span data-stu-id="17c28-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="17c28-262">Для моделирования функциональных возможностей приложения в качестве набора ресурсов, в которых операции представлены [HTTP-командами](#verb), интерфейсы API-интерфейса должны использовать маршрутизацию атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="17c28-263">При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="17c28-264">Следующий `StartUp.Configure` код является типичным для REST API и используется в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="17c28-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="17c28-265">В приведенном выше коде <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> вызывается внутри, `UseEndpoints` чтобы сопоставлять атрибуты, перенаправляемые контроллерами.</span><span class="sxs-lookup"><span data-stu-id="17c28-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="17c28-266">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="17c28-266">In the following example:</span></span>

* <span data-ttu-id="17c28-267">Используется предыдущий `Configure` метод.</span><span class="sxs-lookup"><span data-stu-id="17c28-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="17c28-268">`HomeController` совпадает с набором URL-адресов, похожим на стандартный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="17c28-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="17c28-269">`HomeController.Index`Действие выполняется для любого из URL-путей,, `/` `/Home` `/Home/Index` или `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="17c28-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="17c28-270">В этом примере демонстрируется ключевое различие в программировании между маршрутизацией атрибутов и [обычной маршрутизацией](#cr).</span><span class="sxs-lookup"><span data-stu-id="17c28-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="17c28-271">Маршрутизация атрибутов требует больше входных данных для указания маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="17c28-272">Стандартный маршрут по умолчанию обрабатывает маршруты более кратко.</span><span class="sxs-lookup"><span data-stu-id="17c28-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="17c28-273">Однако маршрутизация атрибутов разрешает и требует точного контроля над тем, какие шаблоны маршрутов применяются к каждому [действию](#action).</span><span class="sxs-lookup"><span data-stu-id="17c28-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="17c28-274">При использовании маршрутизации атрибутов контроллер и имена действий воспроизводятся, если не используется [замена токенов](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="17c28-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="17c28-275">В следующем примере совпадают те же URL-адреса, что и в предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="17c28-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="17c28-276">В следующем коде используется замена токенов для `action` и `controller` :</span><span class="sxs-lookup"><span data-stu-id="17c28-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="17c28-277">Следующий код применяется `[Route("[controller]/[action]")]` к контроллеру:</span><span class="sxs-lookup"><span data-stu-id="17c28-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="17c28-278">В приведенном выше коде `Index` шаблоны методов должны находиться в начале `/` или `~/` в шаблонах маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="17c28-279">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="17c28-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="17c28-280">Сведения о выборе шаблона маршрутов см. в разделе [приоритет шаблона маршрута](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="17c28-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="17c28-281">Зарезервированные имена маршрутизации</span><span class="sxs-lookup"><span data-stu-id="17c28-281">Reserved routing names</span></span>

<span data-ttu-id="17c28-282">Следующие ключевые слова являются зарезервированными именами параметров маршрута при использовании контроллеров или Razor страниц:</span><span class="sxs-lookup"><span data-stu-id="17c28-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="17c28-283">Использование `page` в качестве параметра маршрута с маршрутизацией атрибутов является распространенной ошибкой.</span><span class="sxs-lookup"><span data-stu-id="17c28-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="17c28-284">Это приводит к несогласованности и путанице при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="17c28-285">Специальные имена параметров используются при формировании URL-адресов, чтобы определить, относится ли операция создания URL-адреса к Razor странице или к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="17c28-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="17c28-286">Шаблоны HTTP-команд</span><span class="sxs-lookup"><span data-stu-id="17c28-286">HTTP verb templates</span></span>

<span data-ttu-id="17c28-287">ASP.NET Core содержит следующие шаблоны HTTP-команд:</span><span class="sxs-lookup"><span data-stu-id="17c28-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="17c28-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="17c28-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="17c28-290">[[Хттппут]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="17c28-291">[[Хттпделете]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="17c28-292">[[Хттфеад]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="17c28-293">[[Хттппатч]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="17c28-294">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-294">Route templates</span></span>

<span data-ttu-id="17c28-295">ASP.NET Core имеет следующие шаблоны маршрутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="17c28-296">Все [шаблоны HTTP-команд](#verb) являются шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="17c28-297">[Направлены](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="17c28-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="17c28-298">Маршрутизация атрибутов с помощью атрибутов глагола HTTP</span><span class="sxs-lookup"><span data-stu-id="17c28-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="17c28-299">Рассмотрим следующий контроллер:</span><span class="sxs-lookup"><span data-stu-id="17c28-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="17c28-300">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="17c28-300">In the preceding code:</span></span>

* <span data-ttu-id="17c28-301">Каждое действие содержит `[HttpGet]` атрибут, который ограничивает сопоставление только запросами HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="17c28-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="17c28-302">`GetProduct`Действие включает `"{id}"` шаблон, поэтому `id` добавляется к `"api/[controller]"` шаблону на контроллере.</span><span class="sxs-lookup"><span data-stu-id="17c28-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="17c28-303">Шаблон методов — `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="17c28-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="17c28-304">Поэтому это действие соответствует только запросам Get для формы `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` и т. д.</span><span class="sxs-lookup"><span data-stu-id="17c28-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="17c28-305">`GetIntProduct`Действие содержит `"int/{id:int}")` шаблон.</span><span class="sxs-lookup"><span data-stu-id="17c28-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="17c28-306">`:int`Часть шаблона ограничивает `id` значения маршрута строками, которые могут быть преобразованы в целые числа.</span><span class="sxs-lookup"><span data-stu-id="17c28-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="17c28-307">Запрос GET к `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="17c28-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="17c28-308">Не соответствует этому действию.</span><span class="sxs-lookup"><span data-stu-id="17c28-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="17c28-309">Возвращает ошибку [404 не найден](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="17c28-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="17c28-310">`GetInt2Product`Действие содержит `{id}` в шаблоне, но не ограничивает `id` значения, которые можно преобразовать в целое число.</span><span class="sxs-lookup"><span data-stu-id="17c28-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="17c28-311">Запрос GET к `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="17c28-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="17c28-312">Соответствует этому маршруту.</span><span class="sxs-lookup"><span data-stu-id="17c28-312">Matches this route.</span></span>
  * <span data-ttu-id="17c28-313">Привязка модели не может преобразовать `abc` в целое число.</span><span class="sxs-lookup"><span data-stu-id="17c28-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="17c28-314">`id`Параметр метода имеет тип Integer.</span><span class="sxs-lookup"><span data-stu-id="17c28-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="17c28-315">Возвращает [400 Неверный запрос](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , так как привязка модели не смогла преобразовать `abc` в целое число.</span><span class="sxs-lookup"><span data-stu-id="17c28-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="17c28-316">Маршрутизация атрибутов может использовать <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> такие атрибуты <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , как, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> и <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="17c28-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="17c28-317">Все атрибуты [HTTP-команды](#verb) принимают шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="17c28-318">В следующем примере показаны два действия, которые соответствуют одному шаблону маршрута:</span><span class="sxs-lookup"><span data-stu-id="17c28-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="17c28-319">Использование URL-пути `/products3` :</span><span class="sxs-lookup"><span data-stu-id="17c28-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="17c28-320">`MyProductsController.ListProducts`Действие выполняется, когда [HTTP-команда](#verb) имеет значение `GET` .</span><span class="sxs-lookup"><span data-stu-id="17c28-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="17c28-321">`MyProductsController.CreateProduct`Действие выполняется, когда [HTTP-команда](#verb) имеет значение `POST` .</span><span class="sxs-lookup"><span data-stu-id="17c28-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="17c28-322">При создании REST API в редких случаях необходимо использовать `[Route(...)]` метод действия, поскольку действие принимает все методы HTTP.</span><span class="sxs-lookup"><span data-stu-id="17c28-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="17c28-323">Лучше использовать более конкретный [атрибут HTTP-команды](#verb) , чтобы точно определить, что поддерживает API.</span><span class="sxs-lookup"><span data-stu-id="17c28-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="17c28-324">Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.</span><span class="sxs-lookup"><span data-stu-id="17c28-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="17c28-325">Для моделирования функциональных возможностей приложения в качестве набора ресурсов, в которых операции представлены HTTP-командами, интерфейсы API-интерфейса должны использовать маршрутизацию атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="17c28-326">Это означает, что многие операции, например GET и POST для одного и того же логического ресурса, используют один и тот же URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="17c28-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="17c28-327">Маршрутизация с помощью атрибутов обеспечивает необходимый уровень контроля, позволяющий тщательно разработать схему общедоступных конечных точек API-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="17c28-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="17c28-328">Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="17c28-329">В следующем примере требуется в `id` качестве части URL-пути:</span><span class="sxs-lookup"><span data-stu-id="17c28-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="17c28-330">`Products2ApiController.GetProduct(int)`Действие:</span><span class="sxs-lookup"><span data-stu-id="17c28-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="17c28-331">Выполняется с URL-путем, например `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="17c28-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="17c28-332">Не выполняется с URL-адресом `/products2` .</span><span class="sxs-lookup"><span data-stu-id="17c28-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="17c28-333">Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов.</span><span class="sxs-lookup"><span data-stu-id="17c28-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="17c28-334">Дополнительные сведения см. [в разделе Определение поддерживаемых типов содержимого запросов с помощью атрибута использования](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="17c28-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="17c28-335">Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="17c28-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="17c28-336">Дополнительные сведения о см `[ApiController]` . в разделе [атрибут ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="17c28-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="17c28-337">Имя маршрута</span><span class="sxs-lookup"><span data-stu-id="17c28-337">Route name</span></span>

<span data-ttu-id="17c28-338">В следующем коде определяется имя маршрута`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="17c28-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="17c28-339">Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="17c28-340">Имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-340">Route names:</span></span>

* <span data-ttu-id="17c28-341">Не влияют на поведение маршрутизации в соответствии с URL-адресом.</span><span class="sxs-lookup"><span data-stu-id="17c28-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="17c28-342">Используются только для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-342">Are only used for URL generation.</span></span>

<span data-ttu-id="17c28-343">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="17c28-344">Сравните предыдущий код с обычным маршрутом по умолчанию, который определяет `id` параметр как необязательный ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="17c28-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="17c28-345">Возможность точного указания интерфейсов API имеет свои преимущества, такие как разрешение `/products` и `/products/5` возможность отправки в различные действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="17c28-346">Объединение маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="17c28-346">Combining attribute routes</span></span>

<span data-ttu-id="17c28-347">Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="17c28-348">Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="17c28-349">В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="17c28-350">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="17c28-350">In the preceding example:</span></span>

* <span data-ttu-id="17c28-351">Путь URL-адреса `/products` может совпадать `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="17c28-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="17c28-352">Путь URL-адреса `/products/5` может совпадать `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="17c28-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="17c28-353">Оба эти действия соответствуют только HTTP `GET` , так как они помечены `[HttpGet]` атрибутом.</span><span class="sxs-lookup"><span data-stu-id="17c28-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="17c28-354">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="17c28-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="17c28-355">Следующий пример соответствует набору URL-путей, аналогичному маршруту по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="17c28-356">В следующей таблице описаны `[Route]` атрибуты в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="17c28-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="17c28-357">attribute</span><span class="sxs-lookup"><span data-stu-id="17c28-357">Attribute</span></span>               | <span data-ttu-id="17c28-358">Объединяет с `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="17c28-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="17c28-359">Определение шаблона маршрута</span><span class="sxs-lookup"><span data-stu-id="17c28-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="17c28-360">Да</span><span class="sxs-lookup"><span data-stu-id="17c28-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="17c28-361">Да</span><span class="sxs-lookup"><span data-stu-id="17c28-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="17c28-362">**Нет**</span><span class="sxs-lookup"><span data-stu-id="17c28-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="17c28-363">Да</span><span class="sxs-lookup"><span data-stu-id="17c28-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="17c28-364">Порядок маршрута атрибута</span><span class="sxs-lookup"><span data-stu-id="17c28-364">Attribute route order</span></span>

<span data-ttu-id="17c28-365">Маршрутизация создает дерево и сопоставляет все конечные точки одновременно:</span><span class="sxs-lookup"><span data-stu-id="17c28-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="17c28-366">Записи маршрутов ведут себя так, как если бы они были размещены в идеальном порядке.</span><span class="sxs-lookup"><span data-stu-id="17c28-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="17c28-367">Наиболее конкретные маршруты могут выполняться до более общих маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="17c28-368">Например, маршрут атрибута, например, `blog/search/{topic}` более специфичен, чем маршрут атрибута, например `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="17c28-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="17c28-369">`blog/search/{topic}`Маршрут имеет более высокий приоритет, чем по умолчанию, так как он является более конкретным.</span><span class="sxs-lookup"><span data-stu-id="17c28-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="17c28-370">При использовании [обычной маршрутизации](#cr)разработчик несет ответственность за размещение маршрутов в нужном порядке.</span><span class="sxs-lookup"><span data-stu-id="17c28-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="17c28-371">Маршруты атрибутов могут настраивать порядок с помощью <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Свойства.</span><span class="sxs-lookup"><span data-stu-id="17c28-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="17c28-372">Все указанные в платформе [атрибуты маршрутов](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) включают `Order` .</span><span class="sxs-lookup"><span data-stu-id="17c28-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="17c28-373">Маршруты обрабатываются в порядке возрастания значения свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="17c28-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="17c28-374">Порядок по умолчанию — `0`.</span><span class="sxs-lookup"><span data-stu-id="17c28-374">The default order is `0`.</span></span> <span data-ttu-id="17c28-375">Настройка маршрута с помощью `Order = -1` запусков перед маршрутами, которые не задают порядок.</span><span class="sxs-lookup"><span data-stu-id="17c28-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="17c28-376">Настройка маршрута с помощью команды `Order = 1` выполняется после упорядочения маршрутов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="17c28-377">**Избегайте** в зависимости от `Order` .</span><span class="sxs-lookup"><span data-stu-id="17c28-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="17c28-378">Если для правильной маршрутизации URL-адресу приложения требуются явные значения порядка, скорее всего, это приведет к путанице с клиентами.</span><span class="sxs-lookup"><span data-stu-id="17c28-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="17c28-379">Как правило, маршрутизация атрибутов выбирает правильный маршрут с сопоставлением URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="17c28-380">Если порядок по умолчанию, используемый для создания URL-адреса, не работает, использование имени маршрута в качестве переопределения обычно проще, чем применение `Order` Свойства.</span><span class="sxs-lookup"><span data-stu-id="17c28-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="17c28-381">Рассмотрим следующие два контроллера, которые определяют сопоставление маршрутов `/home` .</span><span class="sxs-lookup"><span data-stu-id="17c28-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="17c28-382">`/home`При запросе с помощью приведенного выше кода создается исключение, аналогичное следующему:</span><span class="sxs-lookup"><span data-stu-id="17c28-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="17c28-383">Добавление `Order` к одному из атрибутов маршрута разрешает неоднозначность:</span><span class="sxs-lookup"><span data-stu-id="17c28-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="17c28-384">В приведенном выше коде `/home` запускает `HomeController.Index` конечную точку.</span><span class="sxs-lookup"><span data-stu-id="17c28-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="17c28-385">Для получения `MyDemoController.MyIndex` запроса `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="17c28-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="17c28-386">**Примечание.**</span><span class="sxs-lookup"><span data-stu-id="17c28-386">**Note**:</span></span>

* <span data-ttu-id="17c28-387">Приведенный выше код представляет собой пример или низкую структуру маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="17c28-388">Он использовался для иллюстрации `Order` Свойства.</span><span class="sxs-lookup"><span data-stu-id="17c28-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="17c28-389">`Order`Свойство разрешает неоднозначность, но этот шаблон не может быть сопоставлен.</span><span class="sxs-lookup"><span data-stu-id="17c28-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="17c28-390">Лучше удалить `[Route("Home")]` шаблон.</span><span class="sxs-lookup"><span data-stu-id="17c28-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="17c28-391">См. страницы соглашения о маршрутах [ Razor и приложениях. порядок](xref:razor-pages/razor-pages-conventions#route-order) маршрута для получения сведений о порядке маршрутов со Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="17c28-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="17c28-392">В некоторых случаях возвращается ошибка HTTP 500 с неоднозначными маршрутами.</span><span class="sxs-lookup"><span data-stu-id="17c28-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="17c28-393">Используйте [ведение журнала](xref:fundamentals/logging/index) , чтобы узнать, какие конечные точки вызывают `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="17c28-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="17c28-394">Замена токенов в шаблонах маршрутов [контроллер], [действие], [область]</span><span class="sxs-lookup"><span data-stu-id="17c28-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="17c28-395">Для удобства маршруты к атрибутам поддерживают *замену маркера* путем заключения маркера в квадратные скобки ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="17c28-395">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="17c28-396">Маркеры `[action]` , `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут.</span><span class="sxs-lookup"><span data-stu-id="17c28-396">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="17c28-397">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="17c28-397">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="17c28-398">Отвечающ `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="17c28-398">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="17c28-399">Отвечающ `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="17c28-399">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="17c28-400">Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-400">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="17c28-401">Предыдущий пример ведет себя так же, как и следующий код:</span><span class="sxs-lookup"><span data-stu-id="17c28-401">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="17c28-402">Маршруты на основе атрибутов могут также сочетаться с наследованием.</span><span class="sxs-lookup"><span data-stu-id="17c28-402">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="17c28-403">Это мощное сочетание с заменой токенов.</span><span class="sxs-lookup"><span data-stu-id="17c28-403">This is powerful combined with token replacement.</span></span> <span data-ttu-id="17c28-404">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-404">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="17c28-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`создает уникальное имя маршрута для каждого действия:</span><span class="sxs-lookup"><span data-stu-id="17c28-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="17c28-406">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-406">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="17c28-407"> создает уникальное имя маршрута для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-407">generates a unique route name for each action.</span></span>

<span data-ttu-id="17c28-408">Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).</span><span class="sxs-lookup"><span data-stu-id="17c28-408">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="17c28-409">Использование преобразователя параметров для настройки замены токенов</span><span class="sxs-lookup"><span data-stu-id="17c28-409">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="17c28-410">Замену токенов можно настроить, используя преобразователь параметров.</span><span class="sxs-lookup"><span data-stu-id="17c28-410">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="17c28-411">Преобразователь параметров реализует <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> и преобразует значения параметров.</span><span class="sxs-lookup"><span data-stu-id="17c28-411">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="17c28-412">Например, `SlugifyParameterTransformer` преобразователь настраиваемых параметров изменяет `SubscriptionManagement` значение маршрута на `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="17c28-412">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="17c28-413"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> является соглашением для модели приложения, которое:</span><span class="sxs-lookup"><span data-stu-id="17c28-413">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="17c28-414">Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.</span><span class="sxs-lookup"><span data-stu-id="17c28-414">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="17c28-415">Настраивает значения токена маршрут атрибута при замене.</span><span class="sxs-lookup"><span data-stu-id="17c28-415">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="17c28-416">Предыдущий `ListAll` метод соответствует `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="17c28-416">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="17c28-417">`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="17c28-417">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="17c28-418">Определение служебной версии см. в [веб-документах MDN в служебной системе](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="17c28-418">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="17c28-419">Несколько маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="17c28-419">Multiple attribute routes</span></span>

<span data-ttu-id="17c28-420">Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию.</span><span class="sxs-lookup"><span data-stu-id="17c28-420">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="17c28-421">Наиболее распространенный случай использования этой возможности — имитация поведения маршрута по умолчанию на основе соглашения, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="17c28-421">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="17c28-422">Размещение нескольких атрибутов маршрута на контроллере означает, что каждый из них объединяется с каждым атрибутом маршрута в методах действия:</span><span class="sxs-lookup"><span data-stu-id="17c28-422">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="17c28-423">Все ограничения маршрута [HTTP-команд](#verb) реализуют `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="17c28-423">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="17c28-424">Когда в действие помещаются несколько атрибутов маршрута, реализующих, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-424">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="17c28-425">Каждое ограничение действия объединяется с шаблоном маршрута, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="17c28-425">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="17c28-426">Использование нескольких маршрутов в действиях может показаться полезным и мощным, поэтому лучше обеспечить базовое и четкое определение пространства URL-адресов вашего приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-426">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="17c28-427">Используйте несколько маршрутов **в действиях** , если это необходимо, например, для поддержки существующих клиентов.</span><span class="sxs-lookup"><span data-stu-id="17c28-427">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="17c28-428">Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="17c28-428">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="17c28-429">Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="17c28-429">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="17c28-430">В приведенном выше коде `[HttpPost("product14/{id:int}")]` применяет ограничение маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-430">In the preceding code, `[HttpPost("product14/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="17c28-431">`Products14Controller.ShowProduct`Действие сопоставляется только с URL-путями, такими как `/product14/3` .</span><span class="sxs-lookup"><span data-stu-id="17c28-431">The `Products14Controller.ShowProduct` action is matched only by URL paths like `/product14/3`.</span></span> <span data-ttu-id="17c28-432">Часть шаблона маршрута `{id:int}` ограничивает этот сегмент только целыми числами.</span><span class="sxs-lookup"><span data-stu-id="17c28-432">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="17c28-433">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="17c28-433">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="17c28-434">Настраиваемые атрибуты маршрута с помощью Ираутетемплатепровидер</span><span class="sxs-lookup"><span data-stu-id="17c28-434">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="17c28-435">Все [атрибуты маршрута](#rt) реализуют <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="17c28-435">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="17c28-436">Среда выполнения ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="17c28-436">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="17c28-437">Ищет атрибуты классов контроллеров и методов действий при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-437">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="17c28-438">Использует атрибуты, реализующие `IRouteTemplateProvider` для создания начального набора маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-438">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="17c28-439">Реализуйте `IRouteTemplateProvider` , чтобы определить пользовательские атрибуты маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-439">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="17c28-440">Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.</span><span class="sxs-lookup"><span data-stu-id="17c28-440">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="17c28-441">Предыдущий `Get` метод возвращает значение `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="17c28-441">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="17c28-442">Использование модели приложения для настройки маршрутов к атрибутам</span><span class="sxs-lookup"><span data-stu-id="17c28-442">Use application model to customize attribute routes</span></span>

<span data-ttu-id="17c28-443">Модель приложения:</span><span class="sxs-lookup"><span data-stu-id="17c28-443">The application model:</span></span>

* <span data-ttu-id="17c28-444">Объектная модель, создаваемая при запуске.</span><span class="sxs-lookup"><span data-stu-id="17c28-444">Is an object model created at startup.</span></span>
* <span data-ttu-id="17c28-445">Содержит все метаданные, используемые ASP.NET Core для маршрутизации и выполнения действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="17c28-445">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="17c28-446">Модель приложения включает все данные, собранные из атрибутов маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-446">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="17c28-447">Данные из атрибутов маршрута предоставляются `IRouteTemplateProvider` реализацией.</span><span class="sxs-lookup"><span data-stu-id="17c28-447">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="17c28-448">Именован</span><span class="sxs-lookup"><span data-stu-id="17c28-448">Conventions:</span></span>

* <span data-ttu-id="17c28-449">Можно написать, чтобы изменить модель приложения, чтобы настроить маршрутизацию.</span><span class="sxs-lookup"><span data-stu-id="17c28-449">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="17c28-450">Считываются при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-450">Are read at app startup.</span></span>

<span data-ttu-id="17c28-451">В этом разделе показан простой пример настройки маршрутизации с помощью модели приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-451">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="17c28-452">Следующий код делает маршруты примерно в соответствии со структурой папок проекта.</span><span class="sxs-lookup"><span data-stu-id="17c28-452">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="17c28-453">Следующий код предотвращает `namespace` применение соглашения к контроллерам, которые передаются по атрибуту:</span><span class="sxs-lookup"><span data-stu-id="17c28-453">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="17c28-454">Например, следующий контроллер не использует `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="17c28-454">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="17c28-455">Метод `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="17c28-455">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="17c28-456">Не выполняет никаких действий, если контроллер является перенаправляемым атрибутом.</span><span class="sxs-lookup"><span data-stu-id="17c28-456">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="17c28-457">Задает шаблон контроллеров, основанный на `namespace` , с `namespace` удалением базы.</span><span class="sxs-lookup"><span data-stu-id="17c28-457">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="17c28-458">`NamespaceRoutingConvention`Можно применять в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="17c28-458">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="17c28-459">Например, рассмотрим следующий контроллер:</span><span class="sxs-lookup"><span data-stu-id="17c28-459">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="17c28-460">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="17c28-460">In the preceding code:</span></span>

* <span data-ttu-id="17c28-461">Основанием `namespace` является `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="17c28-461">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="17c28-462">Полное имя предыдущего контроллера — `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="17c28-462">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="17c28-463">`NamespaceRoutingConvention`Задает шаблон контроллеров `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="17c28-463">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="17c28-464">`NamespaceRoutingConvention`Также можно применить в качестве атрибута в контроллере:</span><span class="sxs-lookup"><span data-stu-id="17c28-464">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="17c28-465">Смешанная маршрутизация с помощью атрибутов и на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="17c28-465">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="17c28-466">ASP.NET Core приложения могут сочетать использование обычной маршрутизации и маршрутизации атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-466">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="17c28-467">Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.</span><span class="sxs-lookup"><span data-stu-id="17c28-467">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="17c28-468">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-468">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="17c28-469">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-469">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="17c28-470">Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="17c28-470">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="17c28-471">**Любой** атрибут маршрута на контроллере делает **все** действия в атрибуте Controller перенаправлены.</span><span class="sxs-lookup"><span data-stu-id="17c28-471">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="17c28-472">Маршрутизация атрибутов и Обычная маршрутизация используют один и тот же механизм маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-472">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="17c28-473">Создание URL-адресов и значения окружения</span><span class="sxs-lookup"><span data-stu-id="17c28-473">URL Generation and ambient values</span></span>

<span data-ttu-id="17c28-474">Приложения могут использовать функции создания URL-адресов маршрутизации для создания URL-ссылок на действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-474">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="17c28-475">Создание URL-адресов устраняет прописано URL-адреса, делая код более надежным и сопровождаемым.</span><span class="sxs-lookup"><span data-stu-id="17c28-475">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="17c28-476">В этом разделе рассматриваются функции создания URL-адресов, предоставляемые MVC, и описываются только основные принципы работы формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-476">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="17c28-477">Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="17c28-477">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="17c28-478"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Интерфейс является базовым элементом инфраструктуры между MVC и маршрутизацией для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-478">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="17c28-479">Экземпляр `IUrlHelper` доступен через `Url` свойство в контроллерах, представлениях и компонентах представления.</span><span class="sxs-lookup"><span data-stu-id="17c28-479">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="17c28-480">В следующем примере `IUrlHelper` интерфейс используется с помощью `Controller.Url` свойства для создания URL-адреса для другого действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-480">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="17c28-481">Если приложение использует стандартный маршрут по умолчанию, значением `url` переменной является строка URL-пути `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="17c28-481">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="17c28-482">Этот путь URL-адреса создается при маршрутизации путем объединения:</span><span class="sxs-lookup"><span data-stu-id="17c28-482">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="17c28-483">Значения маршрута из текущего запроса, которые называются **внешними значениями**.</span><span class="sxs-lookup"><span data-stu-id="17c28-483">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="17c28-484">Значения, передаваемые в `Url.Action` шаблон маршрута и подставляемые этими значениями:</span><span class="sxs-lookup"><span data-stu-id="17c28-484">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="17c28-485">Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения.</span><span class="sxs-lookup"><span data-stu-id="17c28-485">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="17c28-486">Параметр маршрута, который не имеет значения, может:</span><span class="sxs-lookup"><span data-stu-id="17c28-486">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="17c28-487">Используйте значение по умолчанию, если таковое имеется.</span><span class="sxs-lookup"><span data-stu-id="17c28-487">Use a default value if it has one.</span></span>
* <span data-ttu-id="17c28-488">Пропускается, если он необязателен.</span><span class="sxs-lookup"><span data-stu-id="17c28-488">Be skipped if it's optional.</span></span> <span data-ttu-id="17c28-489">Например, `id` из шаблона маршрута `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="17c28-489">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="17c28-490">Создание URL-адреса завершается ошибкой, если ни один из обязательных параметров маршрута не имеет соответствующего значения.</span><span class="sxs-lookup"><span data-stu-id="17c28-490">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="17c28-491">Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.</span><span class="sxs-lookup"><span data-stu-id="17c28-491">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="17c28-492">В предыдущем примере `Url.Action` предполагается [Обычная маршрутизация](#cr).</span><span class="sxs-lookup"><span data-stu-id="17c28-492">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="17c28-493">Создание URL-адреса работает аналогично [маршрутизации атрибутов](#ar), хотя понятия различны.</span><span class="sxs-lookup"><span data-stu-id="17c28-493">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="17c28-494">С обычной маршрутизацией:</span><span class="sxs-lookup"><span data-stu-id="17c28-494">With conventional routing:</span></span>

* <span data-ttu-id="17c28-495">Значения маршрута используются для расширения шаблона.</span><span class="sxs-lookup"><span data-stu-id="17c28-495">The route values are used to expand a template.</span></span>
* <span data-ttu-id="17c28-496">Значения маршрута для `controller` и `action` обычно отображаются в этом шаблоне.</span><span class="sxs-lookup"><span data-stu-id="17c28-496">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="17c28-497">Это работает потому, что URL-адреса, соответствующие маршрутизации, соответствуют соглашению.</span><span class="sxs-lookup"><span data-stu-id="17c28-497">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="17c28-498">В следующем примере используется маршрутизация атрибутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-498">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="17c28-499">`Source`Действие в предыдущем коде создает `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="17c28-499">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="17c28-500"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> был добавлен в ASP.NET Core 3,0 в качестве альтернативы `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="17c28-500"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="17c28-501">`LinkGenerator` предлагает аналогичные, но более гибкие функции.</span><span class="sxs-lookup"><span data-stu-id="17c28-501">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="17c28-502">Каждый метод в `IUrlHelper` имеет также соответствующее семейство методов `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="17c28-502">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="17c28-503">Формирование URL-адресов по имени действия</span><span class="sxs-lookup"><span data-stu-id="17c28-503">Generating URLs by action name</span></span>

<span data-ttu-id="17c28-504">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [линкженератор. жетпасбяктион](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)и все связанные перегрузки предназначены для создания целевой конечной точки путем указания имени контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-504">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="17c28-505">При использовании `Url.Action` значения текущего маршрута для `controller` и `action` предоставляются средой выполнения:</span><span class="sxs-lookup"><span data-stu-id="17c28-505">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="17c28-506">Значение `controller` и `action` являются частью как [значений окружающих](#ambient) элементов, так и значений.</span><span class="sxs-lookup"><span data-stu-id="17c28-506">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="17c28-507">Метод `Url.Action` всегда использует текущие значения `action` и `controller` и создает URL-путь, который направляет в текущее действие.</span><span class="sxs-lookup"><span data-stu-id="17c28-507">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="17c28-508">Маршрутизация пытается использовать значения во внешних значениях для заполнения сведений, которые не были предоставлены при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-508">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="17c28-509">Рассмотрим маршрут, аналогичный `{a}/{b}/{c}/{d}` значениям окружения `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="17c28-509">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="17c28-510">Маршрутизация содержит достаточно информации для создания URL-адреса без дополнительных значений.</span><span class="sxs-lookup"><span data-stu-id="17c28-510">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="17c28-511">Маршрутизация содержит достаточно информации, так как все параметры маршрута имеют значение.</span><span class="sxs-lookup"><span data-stu-id="17c28-511">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="17c28-512">Если значение `{ d = Donovan }` Добавлено:</span><span class="sxs-lookup"><span data-stu-id="17c28-512">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="17c28-513">Значение `{ d = David }` игнорируется.</span><span class="sxs-lookup"><span data-stu-id="17c28-513">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="17c28-514">Путь к созданному URL-адресу — `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="17c28-514">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="17c28-515">**Предупреждение**: URL-пути являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="17c28-515">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="17c28-516">В предыдущем примере, если `{ c = Cheryl }` добавляется значение:</span><span class="sxs-lookup"><span data-stu-id="17c28-516">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="17c28-517">Оба значения `{ c = Carol, d = David }` игнорируются.</span><span class="sxs-lookup"><span data-stu-id="17c28-517">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="17c28-518">Больше нет значения для `d` , и создание URL-адреса завершается неудачей.</span><span class="sxs-lookup"><span data-stu-id="17c28-518">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="17c28-519">Необходимо указать требуемые значения `c` и `d` для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-519">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="17c28-520">Возможно, вы намерены столкнуться с этой проблемой с маршрутом по умолчанию `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="17c28-520">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="17c28-521">Эта проблема возникает редко, поскольку `Url.Action` всегда явно указывает `controller` `action` значение и.</span><span class="sxs-lookup"><span data-stu-id="17c28-521">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="17c28-522">Несколько перегрузок [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) принимают объект значений маршрута, чтобы предоставить значения для параметров маршрута, отличных `controller` от `action` и.</span><span class="sxs-lookup"><span data-stu-id="17c28-522">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="17c28-523">Объект значений маршрута часто используется с `id` .</span><span class="sxs-lookup"><span data-stu-id="17c28-523">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="17c28-524">Например, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="17c28-524">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="17c28-525">Объект значений маршрута:</span><span class="sxs-lookup"><span data-stu-id="17c28-525">The route values object:</span></span>

* <span data-ttu-id="17c28-526">По соглашению обычно является объектом анонимного типа.</span><span class="sxs-lookup"><span data-stu-id="17c28-526">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="17c28-527">Может быть `IDictionary<>` или [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="17c28-527">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="17c28-528">Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="17c28-528">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="17c28-529">Приведенный выше код создает `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="17c28-529">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="17c28-530">Следующий код создает абсолютный URL-адрес:</span><span class="sxs-lookup"><span data-stu-id="17c28-530">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="17c28-531">Чтобы создать абсолютный URL-адрес, используйте один из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="17c28-531">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="17c28-532">Перегрузка, принимающая `protocol` .</span><span class="sxs-lookup"><span data-stu-id="17c28-532">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="17c28-533">Например, приведенный выше код.</span><span class="sxs-lookup"><span data-stu-id="17c28-533">For example, the preceding code.</span></span>
* <span data-ttu-id="17c28-534">[Линкженератор. жетурибяктион](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), который по умолчанию создает абсолютные URI.</span><span class="sxs-lookup"><span data-stu-id="17c28-534">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="17c28-535">Создание URL-адресов по маршруту</span><span class="sxs-lookup"><span data-stu-id="17c28-535">Generate URLs by route</span></span>

<span data-ttu-id="17c28-536">Приведенный выше код демонстрирует создание URL-адреса путем передачи контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-536">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="17c28-537">`IUrlHelper` также предоставляет семейство методов [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="17c28-537">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="17c28-538">Эти методы похожи на [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), но не копируют текущие значения `action` и `controller` в значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-538">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="17c28-539">Наиболее распространенное использование `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="17c28-539">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="17c28-540">Указывает имя маршрута для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-540">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="17c28-541">Обычно не указывает имя контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-541">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="17c28-542">Следующий Razor файл создает ссылку HTML на `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="17c28-542">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="17c28-543">Создание URL-адресов в HTML и Razor</span><span class="sxs-lookup"><span data-stu-id="17c28-543">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="17c28-544"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> предоставляет <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> методы [HTML. Бегинформ](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) и [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) для создания `<form>` элементов и `<a>` соответственно.</span><span class="sxs-lookup"><span data-stu-id="17c28-544"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="17c28-545">Эти методы используют метод [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) для создания URL-адреса и принимают аналогичные аргументы.</span><span class="sxs-lookup"><span data-stu-id="17c28-545">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="17c28-546">Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.</span><span class="sxs-lookup"><span data-stu-id="17c28-546">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="17c28-547">Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`.</span><span class="sxs-lookup"><span data-stu-id="17c28-547">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="17c28-548">Обе они реализуются с помощью интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="17c28-548">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="17c28-549">Дополнительные сведения см. [в разделе вспомогательные функции тегов в формах](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="17c28-549">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="17c28-550">Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.</span><span class="sxs-lookup"><span data-stu-id="17c28-550">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="17c28-551">Создание URL-адресов в результатах действий</span><span class="sxs-lookup"><span data-stu-id="17c28-551">URL generation in Action Results</span></span>

<span data-ttu-id="17c28-552">В предыдущих примерах было показано использование `IUrlHelper` в контроллере.</span><span class="sxs-lookup"><span data-stu-id="17c28-552">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="17c28-553">Наиболее распространенным использованием контроллера является создание URL-адреса как части результата действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-553">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="17c28-554">Базовые классы <xref:Microsoft.AspNetCore.Mvc.ControllerBase> и <xref:Microsoft.AspNetCore.Mvc.Controller> предоставляют удобные методы для результатов действий, ссылающихся на другое действие.</span><span class="sxs-lookup"><span data-stu-id="17c28-554">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="17c28-555">Один из типичных способов использования — перенаправление после приема входных данных пользователя:</span><span class="sxs-lookup"><span data-stu-id="17c28-555">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="17c28-556">Действия, выполняемые фабриками, такие как <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> и, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> следуют аналогичному шаблону для методов в `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="17c28-556">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="17c28-557">Выделенные маршруты на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="17c28-557">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="17c28-558">В [обычной маршрутизации](#cr) может использоваться особый тип определения маршрута, называемый [выделенным обычным маршрутом](#dcr).</span><span class="sxs-lookup"><span data-stu-id="17c28-558">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="17c28-559">В следующем примере маршрут с именем `blog` является выделенным обычным маршрутом:</span><span class="sxs-lookup"><span data-stu-id="17c28-559">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="17c28-560">Используя приведенные выше определения маршрутов, `Url.Action("Index", "Home")` создает URL-путь `/` с помощью `default` маршрута, но почему?</span><span class="sxs-lookup"><span data-stu-id="17c28-560">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="17c28-561">Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="17c28-561">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="17c28-562">[Выделенные традиционные маршруты](#dcr) полагаются на особое поведение значений по умолчанию, которые не имеют соответствующего параметра маршрута, который предотвращает слишком [жадную](xref:fundamentals/routing#greedy) маршрутизацию при формировании URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-562">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="17c28-563">В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет.</span><span class="sxs-lookup"><span data-stu-id="17c28-563">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="17c28-564">Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-564">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="17c28-565">Создание URL-адреса с помощью `blog` завершается ошибкой, так как значения `{ controller = Home, action = Index }` не совпадают `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="17c28-565">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="17c28-566">После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="17c28-566">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="17c28-567">Области</span><span class="sxs-lookup"><span data-stu-id="17c28-567">Areas</span></span>

<span data-ttu-id="17c28-568">[Области](xref:mvc/controllers/areas) — это функция MVC, используемая для упорядочивания связанных функций в группе в виде отдельных:</span><span class="sxs-lookup"><span data-stu-id="17c28-568">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="17c28-569">Пространство имен маршрутизации для действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="17c28-569">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="17c28-570">Структура папок для представлений.</span><span class="sxs-lookup"><span data-stu-id="17c28-570">Folder structure for views.</span></span>

<span data-ttu-id="17c28-571">Использование областей позволяет приложению иметь несколько контроллеров с одинаковым именем, если они имеют разные области.</span><span class="sxs-lookup"><span data-stu-id="17c28-571">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="17c28-572">При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`.</span><span class="sxs-lookup"><span data-stu-id="17c28-572">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="17c28-573">В этом разделе обсуждается взаимодействие маршрутизации с областями.</span><span class="sxs-lookup"><span data-stu-id="17c28-573">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="17c28-574">Сведения об использовании областей с представлениями см. в разделе [области](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="17c28-574">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="17c28-575">В следующем примере MVC настраивается для использования стандартного маршрута по умолчанию и `area` маршрута для `area` именованного `Blog` :</span><span class="sxs-lookup"><span data-stu-id="17c28-575">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="17c28-576">В приведенном выше коде <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> вызывается для создания `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="17c28-576">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="17c28-577">Вторым параметром `"Blog"` является имя области.</span><span class="sxs-lookup"><span data-stu-id="17c28-577">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="17c28-578">При сопоставлении URL-пути `/Manage/Users/AddUser` , например, `"blog_route"` маршрут формирует значения маршрута `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="17c28-578">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="17c28-579">`area`Значение маршрута создается значением по умолчанию для `area` .</span><span class="sxs-lookup"><span data-stu-id="17c28-579">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="17c28-580">Маршрут, созданный с помощью, `MapAreaControllerRoute` эквивалентен следующему:</span><span class="sxs-lookup"><span data-stu-id="17c28-580">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="17c28-581">Метод `MapAreaControllerRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`).</span><span class="sxs-lookup"><span data-stu-id="17c28-581">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="17c28-582">Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-582">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="17c28-583">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="17c28-583">Conventional routing is order-dependent.</span></span> <span data-ttu-id="17c28-584">Как правило, маршруты с областями следует размещать раньше, так как они более специфичны, чем маршруты без области.</span><span class="sxs-lookup"><span data-stu-id="17c28-584">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="17c28-585">Используя предыдущий пример, значения маршрута `{ area = Blog, controller = Users, action = AddUser }` соответствуют следующему действию:</span><span class="sxs-lookup"><span data-stu-id="17c28-585">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="17c28-586">Атрибут [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) указывает, что контроллер является частью области.</span><span class="sxs-lookup"><span data-stu-id="17c28-586">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="17c28-587">Этот контроллер находится в `Blog` области.</span><span class="sxs-lookup"><span data-stu-id="17c28-587">This controller is in the `Blog` area.</span></span> <span data-ttu-id="17c28-588">Контроллеры без `[Area]` атрибута не являются членами какой-либо области и не  совпадают, если `area` значение маршрута предоставляется службой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-588">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="17c28-589">В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-589">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="17c28-590">Пространство имен каждого контроллера показано здесь для полноты.</span><span class="sxs-lookup"><span data-stu-id="17c28-590">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="17c28-591">Если предыдущие контроллеры использовали одно и то же пространство имен, создается ошибка компилятора.</span><span class="sxs-lookup"><span data-stu-id="17c28-591">If the preceding controllers used the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="17c28-592">Имена пространств классов не влияют на маршрутизацию в MVC.</span><span class="sxs-lookup"><span data-stu-id="17c28-592">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="17c28-593">Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="17c28-593">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="17c28-594">Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-594">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="17c28-595">В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.</span><span class="sxs-lookup"><span data-stu-id="17c28-595">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="17c28-596">При выполнении действия внутри области значение маршрута для `area` будет доступно как [значение окружения](#ambient) для маршрутизации, используемой для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-596">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="17c28-597">Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="17c28-597">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="17c28-598">Следующий код создает URL-адрес для `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="17c28-598">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="17c28-599">Определение действия</span><span class="sxs-lookup"><span data-stu-id="17c28-599">Action definition</span></span>

<span data-ttu-id="17c28-600">Открытые методы на контроллере, за исключением тех, которые имеют атрибут [недействия](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , являются действиями.</span><span class="sxs-lookup"><span data-stu-id="17c28-600">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="17c28-601">Образец кода</span><span class="sxs-lookup"><span data-stu-id="17c28-601">Sample code</span></span>

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* <span data-ttu-id="17c28-602">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17c28-602">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17c28-603">В ASP.NET Core MVC используется [ПО промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов с действиями.</span><span class="sxs-lookup"><span data-stu-id="17c28-603">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="17c28-604">Маршруты определяются в коде запуска или атрибутах.</span><span class="sxs-lookup"><span data-stu-id="17c28-604">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="17c28-605">Они описывают то, как пути URL-адресов должны сопоставляться с действиями.</span><span class="sxs-lookup"><span data-stu-id="17c28-605">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="17c28-606">С помощью маршрутов также формируются URL-адреса (для ссылок), отправляемые в ответах.</span><span class="sxs-lookup"><span data-stu-id="17c28-606">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="17c28-607">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-607">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="17c28-608">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-608">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="17c28-609">Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="17c28-609">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="17c28-610">В этом документе описывается взаимодействие между MVC и маршрутизацией и использование возможностей маршрутизации в типичных приложениях MVC.</span><span class="sxs-lookup"><span data-stu-id="17c28-610">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="17c28-611">Подробные сведения о расширенной маршрутизации см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="17c28-611">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="17c28-612">Настройка ПО промежуточного слоя маршрутизации</span><span class="sxs-lookup"><span data-stu-id="17c28-612">Setting up Routing Middleware</span></span>

<span data-ttu-id="17c28-613">Метод *Configure* содержит код, который выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="17c28-613">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="17c28-614">В вызове `UseMvc` метод `MapRoute` используется для создания одного маршрута, который называется маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="17c28-614">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="17c28-615">В большинстве приложений MVC используется маршрут с шаблоном, сходным с маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="17c28-615">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="17c28-616">Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` может сопоставлять путь URL-адреса, например `/Products/Details/5`, и извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем разбивки пути на лексемы.</span><span class="sxs-lookup"><span data-stu-id="17c28-616">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="17c28-617">MVC попытается найти контроллер с именем `ProductsController` и выполнить действие `Details`:</span><span class="sxs-lookup"><span data-stu-id="17c28-617">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="17c28-618">Обратите внимание на то, что в этом примере привязка модели будет использовать значение `id = 5`, чтобы присвоить параметру `id` значение `5` при вызове действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-618">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="17c28-619">Дополнительные сведения см. в разделе [Привязка модели](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="17c28-619">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="17c28-620">Использование маршрута `default`:</span><span class="sxs-lookup"><span data-stu-id="17c28-620">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="17c28-621">Шаблон маршрута:</span><span class="sxs-lookup"><span data-stu-id="17c28-621">The route template:</span></span>

* <span data-ttu-id="17c28-622">`{controller=Home}` определяет `Home` в качестве объекта `controller` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-622">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="17c28-623">`{action=Index}` определяет `Index` в качестве объекта `action` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-623">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="17c28-624">`{id?}` определяет необязательный параметр `id`.</span><span class="sxs-lookup"><span data-stu-id="17c28-624">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="17c28-625">Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="17c28-625">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="17c28-626">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="17c28-626">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="17c28-627">`"{controller=Home}/{action=Index}/{id?}"` может сопоставляться с путем URL-адреса `/` и выдает значения маршрута `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-627">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="17c28-628">Для объектов `controller` и `action` используются значения по умолчанию. `id` не имеет значения, так как в пути URL-адреса нет соответствующего сегмента.</span><span class="sxs-lookup"><span data-stu-id="17c28-628">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="17c28-629">MVC будет использовать эти значения маршрута для выбора действия `HomeController` и `Index`:</span><span class="sxs-lookup"><span data-stu-id="17c28-629">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="17c28-630">При использовании этого определения контроллера и шаблона маршрута действие `HomeController.Index` будет выполняться для любых из следующих путей URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="17c28-630">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="17c28-631">Универсальный метод `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="17c28-631">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="17c28-632">Можно использовать вместо следующего метода:</span><span class="sxs-lookup"><span data-stu-id="17c28-632">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="17c28-633">`UseMvc` и `UseMvcWithDefaultRoute` добавляют экземпляр `RouterMiddleware` в конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="17c28-633">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="17c28-634">MVC не взаимодействует с ПО промежуточного слоя напрямую, а использует маршрутизацию для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="17c28-634">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="17c28-635">MVC подключается к маршрутам посредством экземпляра `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="17c28-635">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="17c28-636">Код метода `UseMvc` имеет примерно следующий вид:</span><span class="sxs-lookup"><span data-stu-id="17c28-636">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="17c28-637">Метод `UseMvc` не определяет маршруты напрямую, а добавляет заполнитель в коллекцию маршрутов для маршрута `attribute`.</span><span class="sxs-lookup"><span data-stu-id="17c28-637">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="17c28-638">Перегрузка `UseMvc(Action<IRouteBuilder>)` позволяет добавлять собственные маршруты, а также поддерживает маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-638">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="17c28-639">Метод `UseMvc` и все его варианты добавляют заполнитель для маршрута на основе атрибутов. Маршрутизация с помощью атрибутов доступна всегда вне зависимости от того, как настроен метод `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="17c28-639">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="17c28-640">Метод `UseMvcWithDefaultRoute` определяет маршрут по умолчанию и поддерживает маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-640">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="17c28-641">В разделе [Маршрутизация с помощью атрибутов](#attribute-routing-ref-label) приводятся более подробные сведения о маршрутизации с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-641">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="17c28-642">Маршрутизация на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="17c28-642">Conventional routing</span></span>

<span data-ttu-id="17c28-643">Маршрут `default`:</span><span class="sxs-lookup"><span data-stu-id="17c28-643">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="17c28-644">Приведенный выше код является примером обычной маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-644">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="17c28-645">Этот стиль называется обычной маршрутизацией, так как он устанавливает *соглашение* для URL-путей:</span><span class="sxs-lookup"><span data-stu-id="17c28-645">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="17c28-646">Первый сегмент пути сопоставляется с именем контроллера.</span><span class="sxs-lookup"><span data-stu-id="17c28-646">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="17c28-647">Второй сопоставляется с именем действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-647">The second maps to the action name.</span></span>
* <span data-ttu-id="17c28-648">Третий сегмент используется для необязательного `id` .</span><span class="sxs-lookup"><span data-stu-id="17c28-648">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="17c28-649">`id` сопоставляется с сущностью модели.</span><span class="sxs-lookup"><span data-stu-id="17c28-649">`id` maps to a model entity.</span></span>

<span data-ttu-id="17c28-650">При использовании этого маршрута `default` путь URL-адреса `/Products/List` сопоставляется с действием `ProductsController.List`, а путь `/Blog/Article/17` сопоставляется с `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="17c28-650">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="17c28-651">Такое сопоставление основывается **только** на именах контроллера и действия, но не на пространствах имен, расположениях исходных файлов или параметрах метода.</span><span class="sxs-lookup"><span data-stu-id="17c28-651">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="17c28-652">Использование маршрутизации на основе соглашений с маршрутом по умолчанию позволяет быстро создавать приложение, не придумывая новый шаблон URL-адреса для каждого определяемого действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-652">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="17c28-653">В случае с приложением с действиями в стиле CRUD единообразие URL-адресов для всех контроллеров позволяет упростить код и сделать пользовательский интерфейс более предсказуемым.</span><span class="sxs-lookup"><span data-stu-id="17c28-653">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="17c28-654">Параметр `id` определяется в шаблоне маршрута как необязательный. Это означает, что действия могут выполняться, даже если идентификатор не указан в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="17c28-654">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="17c28-655">Как правило, если параметр `id` отсутствует в URL-адресе, привязка модели присваивает ему значение `0`, и в результате в базе данных не будет найдена сущность, соответствующая `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="17c28-655">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="17c28-656">Маршрутизация с помощью атрибутов обеспечивает детальный контроль, позволяя настраивать идентификатор как обязательный лишь для некоторых действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-656">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="17c28-657">В документации необязательные параметры, такие как `id`, будут включаться, только если они, скорее всего, могут использоваться в соответствующей ситуации.</span><span class="sxs-lookup"><span data-stu-id="17c28-657">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="17c28-658">Несколько маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-658">Multiple routes</span></span>

<span data-ttu-id="17c28-659">В метод `UseMvc` можно добавить несколько маршрутов, добавив дополнительные вызовы `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="17c28-659">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="17c28-660">Таким образом можно определить несколько соглашений или добавить маршруты на основе соглашений, предназначенные для определенного действия, например:</span><span class="sxs-lookup"><span data-stu-id="17c28-660">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="17c28-661">Маршрут `blog` здесь — это *выделенный маршрут на основе соглашения*. Это означает, что он использует систему маршрутизации на основе соглашений, но предназначен для определенного действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-661">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="17c28-662">Так как параметры `controller` и `action` отсутствуют в шаблоне маршрута, они могут иметь только значения по умолчанию, поэтому этот маршрут всегда будет сопоставляться с действием `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="17c28-662">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="17c28-663">Маршруты в коллекции маршрутов упорядочены и обрабатываются в порядке добавления.</span><span class="sxs-lookup"><span data-stu-id="17c28-663">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="17c28-664">Поэтому в этом примере маршрут `blog` будет проверяться перед маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="17c28-664">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-665">*Выделенные традиционные маршруты* часто используют **перекрестные** параметры маршрута `{*article}` , например для записи оставшейся части пути URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-665">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="17c28-666">Из-за этого маршрут может оказаться слишком универсальным, то есть он будет соответствовать URL-адресам, которые должны сопоставляться с другими маршрутами.</span><span class="sxs-lookup"><span data-stu-id="17c28-666">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="17c28-667">Чтобы избежать этой проблемы, такие универсальные маршруты следует помещать в конце таблицы маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-667">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="17c28-668">Резервирование</span><span class="sxs-lookup"><span data-stu-id="17c28-668">Fallback</span></span>

<span data-ttu-id="17c28-669">В ходе обработки запроса MVC проверяет, можно ли найти контроллер и действие в приложении с помощью предоставленных значений маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-669">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="17c28-670">Если нет действия, соответствующего значениям, маршрут считается не сопоставленным, и проверяется следующий маршрут.</span><span class="sxs-lookup"><span data-stu-id="17c28-670">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="17c28-671">Этот процесс называется *откатом* и призван упростить обработку случаев, когда маршруты на основе соглашений перекрываются.</span><span class="sxs-lookup"><span data-stu-id="17c28-671">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="17c28-672">Разрешение неоднозначности действий</span><span class="sxs-lookup"><span data-stu-id="17c28-672">Disambiguating actions</span></span>

<span data-ttu-id="17c28-673">Если при маршрутизации найдены два соответствующих действия, платформа MVC должна устранить неоднозначность, выбрав наиболее подходящее из них, или создать исключение.</span><span class="sxs-lookup"><span data-stu-id="17c28-673">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="17c28-674">Например:</span><span class="sxs-lookup"><span data-stu-id="17c28-674">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="17c28-675">Этот контроллер определяет два действия, которые соответствуют пути URL-адреса `/Products/Edit/17` и маршрутизируют данные `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-675">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="17c28-676">Это типичный шаблон для контроллеров MVC, в котором метод `Edit(int)` отображает форму для изменения сведений о продукте, а метод `Edit(int, Product)` обрабатывает отправленную форму.</span><span class="sxs-lookup"><span data-stu-id="17c28-676">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="17c28-677">Чтобы это было возможным, платформа MVC должна выбрать `Edit(int, Product)` для HTTP-запроса `POST` и `Edit(int)` для любой другой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="17c28-677">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="17c28-678">`HttpPostAttribute` (`[HttpPost]`) — это реализация интерфейса `IActionConstraint`, которая позволяет выбрать действие, только если HTTP-команда — `POST`.</span><span class="sxs-lookup"><span data-stu-id="17c28-678">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="17c28-679">Наличие интерфейса `IActionConstraint` делает метод `Edit(int, Product)` более подходящим вариантом, чем `Edit(int)`, поэтому `Edit(int, Product)` будет проверяться первым.</span><span class="sxs-lookup"><span data-stu-id="17c28-679">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="17c28-680">Пользовательские реализации `IActionConstraint` потребуется создавать только в особых ситуациях, однако важно понимать роль таких атрибутов, как `HttpPostAttribute`, — аналогичные атрибуты определены для других HTTP-команд.</span><span class="sxs-lookup"><span data-stu-id="17c28-680">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="17c28-681">При маршрутизации на основе соглашений для действий часто используются одинаковые имена в рамках рабочего процесса `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="17c28-681">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="17c28-682">Удобство такого шаблона станет очевидным после ознакомления с разделом [Сведения об интерфейсе IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="17c28-682">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="17c28-683">Если найдено несколько совпадений и MVC не может определить наиболее подходящий маршрут, создается исключение `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="17c28-683">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="17c28-684">Имена маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-684">Route names</span></span>

<span data-ttu-id="17c28-685">Строки `"blog"` и `"default"` в следующих примерах представляют собой имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-685">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="17c28-686">Имя маршрута — это логическое имя, которое позволяет использовать именованный маршрут для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-686">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="17c28-687">Имена значительно упрощают создание URL-адресов, если оно представляет сложность из-за порядка маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-687">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="17c28-688">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-688">Route names must be unique application-wide.</span></span>

<span data-ttu-id="17c28-689">Имена маршрутов не влияют на сопоставление URL-адресов или обработку запросов; они служат только для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-689">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="17c28-690">В статье [Маршрутизация](xref:fundamentals/routing) приводятся более подробные сведения о формировании URL-адресов, в том числе во вспомогательных объектах MVC.</span><span class="sxs-lookup"><span data-stu-id="17c28-690">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="17c28-691">Маршрутизация с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="17c28-691">Attribute routing</span></span>

<span data-ttu-id="17c28-692">При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-692">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="17c28-693">В приведенном ниже примере в методе `Configure` используется `app.UseMvc();`, и маршрут не передается.</span><span class="sxs-lookup"><span data-stu-id="17c28-693">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="17c28-694">`HomeController` будет соответствовать набору URL-адресов, аналогичных тем, которым соответствует маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="17c28-694">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="17c28-695">Действие `HomeController.Index()` будет выполняться для любого из путей URL-адресов `/`, `/Home` или `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="17c28-695">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-696">В этом примере показано ключевое различие между маршрутизацией с помощью атрибутов и маршрутизацией на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="17c28-696">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="17c28-697">При использовании маршрутизации с помощью атрибутов для указания маршрута требуется больше входных данных; маршрут по умолчанию на основе соглашения более лаконичен.</span><span class="sxs-lookup"><span data-stu-id="17c28-697">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="17c28-698">Однако маршрутизация с помощью атрибутов обеспечивает более точный контроль над тем, какие шаблоны маршрутов применяются к каждому действию, и требует такого контроля.</span><span class="sxs-lookup"><span data-stu-id="17c28-698">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="17c28-699">В случае с маршрутизацией с помощью атрибутов имя контроллера и имена действий **не** имеют значения при выборе действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-699">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="17c28-700">В этом примере сопоставление будет производиться с теми же URL-адресами, что и в предыдущем.</span><span class="sxs-lookup"><span data-stu-id="17c28-700">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="17c28-701">Приведенные выше шаблоны маршрутов не определяют параметры маршрутов для `action`, `area` и `controller`.</span><span class="sxs-lookup"><span data-stu-id="17c28-701">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="17c28-702">По сути, эти параметры маршрутов не разрешены в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-702">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="17c28-703">Так как шаблон маршрута уже связан с действием, не имеет смысла анализировать имя действия в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="17c28-703">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="17c28-704">Маршрутизация с помощью атрибутов Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="17c28-704">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="17c28-705">При маршрутизации с помощью атрибутов также могут использоваться атрибуты `Http[Verb]`, такие как `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="17c28-705">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="17c28-706">Все эти атрибуты могут принимать шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-706">All of these attributes can accept a route template.</span></span> <span data-ttu-id="17c28-707">В этом примере показаны два действия, которые совпадают с одним шаблоном маршрута:</span><span class="sxs-lookup"><span data-stu-id="17c28-707">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="17c28-708">Для такого пути URL-адреса, как `/products`, действие `ProductsApi.ListProducts` будет выполнено, если HTTP-командой является `GET`, а действие `ProductsApi.CreateProduct` будет выполнено, если HTTP-командой является `POST`.</span><span class="sxs-lookup"><span data-stu-id="17c28-708">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="17c28-709">При маршрутизации с помощью атрибутов URL-адрес сначала сопоставляется с набором шаблоном маршрутов, определяемых атрибутами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-709">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="17c28-710">После того как найден соответствующий шаблон маршрута, применяются ограничения `IActionConstraint` для определения действий, которые могут быть выполнены.</span><span class="sxs-lookup"><span data-stu-id="17c28-710">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="17c28-711">При разработке REST API редко приходится использовать `[Route(...)]` для метода действия, так как действие принимает все методы HTTP.</span><span class="sxs-lookup"><span data-stu-id="17c28-711">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="17c28-712">Предпочтительнее применять более конкретные атрибуты `Http*Verb*Attributes`, чтобы точно определить поддерживаемые API возможности.</span><span class="sxs-lookup"><span data-stu-id="17c28-712">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="17c28-713">Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.</span><span class="sxs-lookup"><span data-stu-id="17c28-713">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="17c28-714">Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-714">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="17c28-715">В этом примере параметр `id` является обязательным в пути URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-715">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="17c28-716">Действие `ProductsApi.GetProduct(int)` будет выполнено для такого пути URL-адреса, как `/products/3`, но не для такого пути URL-адреса, как `/products`.</span><span class="sxs-lookup"><span data-stu-id="17c28-716">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="17c28-717">Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="17c28-717">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="17c28-718">Имя маршрута</span><span class="sxs-lookup"><span data-stu-id="17c28-718">Route Name</span></span>

<span data-ttu-id="17c28-719">В следующем коде определяется *имя маршрута*`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="17c28-719">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="17c28-720">Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-720">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="17c28-721">Они не влияют на то, как производится сопоставление URL-адресов при маршрутизации, и служат только для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-721">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="17c28-722">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-722">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-723">Сравните это поведение с *маршрутом по умолчанию* на основе соглашения, в котором параметр `id` определяется как необязательный (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="17c28-723">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="17c28-724">Такая возможность точно указывать интерфейсы API имеет преимущества. Например, она позволяет направлять `/products` и `/products/5` в разные действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-724">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="17c28-725">Объединение маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-725">Combining routes</span></span>

<span data-ttu-id="17c28-726">Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-726">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="17c28-727">Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-727">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="17c28-728">В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-728">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="17c28-729">В этом примере путь URL-адреса `/products` может соответствовать `ProductsApi.ListProducts`, а путь URL-адреса `/products/5` — `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="17c28-729">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="17c28-730">Оба эти действия соответствуют только HTTP-запросу `GET`, так как они помечены атрибутом `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="17c28-730">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="17c28-731">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="17c28-731">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="17c28-732">Этот пример соответствует такому же набору путей URL-адресов, что и *маршрут по умолчанию*.</span><span class="sxs-lookup"><span data-stu-id="17c28-732">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="17c28-733">Упорядочение маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="17c28-733">Ordering attribute routes</span></span>

<span data-ttu-id="17c28-734">В отличие от обычных маршрутов, которые выполняются в определенном порядке, маршрутизация атрибутов создает дерево и сопоставляет все маршруты одновременно.</span><span class="sxs-lookup"><span data-stu-id="17c28-734">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="17c28-735">Это равносильно тому, как если бы записи маршрутов находились в идеальном порядке: наиболее конкретные маршруты имеют возможность выполнения перед более общими.</span><span class="sxs-lookup"><span data-stu-id="17c28-735">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="17c28-736">Например, такой маршрут, как `blog/search/{topic}`, является более конкретным по сравнению с `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="17c28-736">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="17c28-737">С логической точки зрения, маршрут `blog/search/{topic}` по умолчанию выполняется первым, так как это единственная разумная очередность.</span><span class="sxs-lookup"><span data-stu-id="17c28-737">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="17c28-738">При использовании маршрутизации на основе соглашений разработчик отвечает за расположение маршрутов в нужном порядке.</span><span class="sxs-lookup"><span data-stu-id="17c28-738">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="17c28-739">При маршрутизации с помощью атрибутов порядок может настраиваться с помощью свойства `Order` всех атрибутов маршрутов, предоставляемых платформой.</span><span class="sxs-lookup"><span data-stu-id="17c28-739">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="17c28-740">Маршруты обрабатываются в порядке возрастания значения свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="17c28-740">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="17c28-741">Порядок по умолчанию — `0`.</span><span class="sxs-lookup"><span data-stu-id="17c28-741">The default order is `0`.</span></span> <span data-ttu-id="17c28-742">Маршрут, для которого задано значение `Order = -1`, будет выполняться перед маршрутами, для которых порядок не задан.</span><span class="sxs-lookup"><span data-stu-id="17c28-742">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="17c28-743">Маршрут, для которого задано значение `Order = 1`, будет выполняться после маршрутов с порядком по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-743">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="17c28-744">Старайтесь не использовать свойство `Order`.</span><span class="sxs-lookup"><span data-stu-id="17c28-744">Avoid depending on `Order`.</span></span> <span data-ttu-id="17c28-745">Если для правильной маршрутизации в пространстве URL-адресов требуются явно заданные значения порядка, скорее всего, это будет вызывать путаницу и в среде клиентов.</span><span class="sxs-lookup"><span data-stu-id="17c28-745">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="17c28-746">Как правило, при маршрутизации с помощью атрибутов правильный маршрут выбирается посредством сопоставления URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-746">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="17c28-747">Если порядок по умолчанию для формирования URL-адресов не работает, использовать имя маршрута в качестве переопределения, как правило, проще, чем применять свойство `Order`.</span><span class="sxs-lookup"><span data-stu-id="17c28-747">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="17c28-748">Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию.</span><span class="sxs-lookup"><span data-stu-id="17c28-748">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="17c28-749">Сведения о порядке маршрутов в Razor разделах страницы см. в разделе [ Razor Маршрутизация и соглашения о приложениях: порядок маршрутов](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="17c28-749">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="17c28-750">Замена токенов в шаблонах маршрутов ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="17c28-750">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="17c28-751">Для удобства маршруты к атрибутам поддерживают *замену маркера* путем заключения маркера в квадратные скобки ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="17c28-751">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="17c28-752">Токены `[action]`, `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут.</span><span class="sxs-lookup"><span data-stu-id="17c28-752">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="17c28-753">В следующем примере действия могут соответствовать путям URL-адресов, как описано в комментариях:</span><span class="sxs-lookup"><span data-stu-id="17c28-753">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="17c28-754">Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-754">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="17c28-755">Приведенный выше пример будет работать так же, как следующий код:</span><span class="sxs-lookup"><span data-stu-id="17c28-755">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="17c28-756">Маршруты на основе атрибутов могут также сочетаться с наследованием.</span><span class="sxs-lookup"><span data-stu-id="17c28-756">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="17c28-757">Эта возможность особенно эффективна при использовании вместе с заменой токенов.</span><span class="sxs-lookup"><span data-stu-id="17c28-757">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="17c28-758">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-758">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="17c28-759">`[Route("[controller]/[action]", Name="[controller]_[action]")]` создает уникальное имя маршрута для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-759">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="17c28-760">Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).</span><span class="sxs-lookup"><span data-stu-id="17c28-760">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="17c28-761">Использование преобразователя параметров для настройки замены токенов</span><span class="sxs-lookup"><span data-stu-id="17c28-761">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="17c28-762">Замену токенов можно настроить, используя преобразователь параметров.</span><span class="sxs-lookup"><span data-stu-id="17c28-762">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="17c28-763">Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров.</span><span class="sxs-lookup"><span data-stu-id="17c28-763">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="17c28-764">Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="17c28-764">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="17c28-765">`RouteTokenTransformerConvention` является соглашением для модели приложения, которое:</span><span class="sxs-lookup"><span data-stu-id="17c28-765">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="17c28-766">Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.</span><span class="sxs-lookup"><span data-stu-id="17c28-766">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="17c28-767">Настраивает значения токена маршрут атрибута при замене.</span><span class="sxs-lookup"><span data-stu-id="17c28-767">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="17c28-768">`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="17c28-768">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="17c28-769">Несколько маршрутов</span><span class="sxs-lookup"><span data-stu-id="17c28-769">Multiple Routes</span></span>

<span data-ttu-id="17c28-770">Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию.</span><span class="sxs-lookup"><span data-stu-id="17c28-770">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="17c28-771">Наиболее распространенный случай использования этой возможности — имитация поведения *маршрута по умолчанию на основе соглашения*, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="17c28-771">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="17c28-772">Добавление нескольких атрибутов маршрута для контроллера означает, что каждый из них будет объединяться с каждым из атрибутов маршрута, определенных для методов действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-772">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="17c28-773">Если несколько атрибутов маршрута (реализующих интерфейс `IActionConstraint`) добавлены для действия, каждое ограничение действия объединяется с шаблоном маршрута из атрибута, в котором оно определено.</span><span class="sxs-lookup"><span data-stu-id="17c28-773">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="17c28-774">Хотя использование нескольких маршрутов к действиям может показаться очень эффективной возможностью, лучше, чтобы пространство URL-адресов приложения оставалось простым и четко организованным.</span><span class="sxs-lookup"><span data-stu-id="17c28-774">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="17c28-775">Используйте несколько маршрутов к действиям только там, где это необходимо, например для поддержки существующих клиентов.</span><span class="sxs-lookup"><span data-stu-id="17c28-775">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="17c28-776">Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="17c28-776">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="17c28-777">Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="17c28-777">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="17c28-778">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="17c28-778">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="17c28-779">Пользовательские атрибуты маршрута с использованием `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="17c28-779">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="17c28-780">Все атрибуты маршрутов, предоставляемые платформой ( `[Route(...)]`, `[HttpGet(...)]` и т. д.), реализуют интерфейс `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="17c28-780">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="17c28-781">MVC ищет атрибуты в классах контроллеров и методах действий при запуске приложения и использует те из них, которые реализуют интерфейс `IRouteTemplateProvider`, для формирования начального набора маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-781">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="17c28-782">Вы можете реализовать интерфейс `IRouteTemplateProvider` для определения собственных атрибутов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-782">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="17c28-783">Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.</span><span class="sxs-lookup"><span data-stu-id="17c28-783">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="17c28-784">Атрибут в приведенном выше примере автоматически присваивает шаблону `Template` значение `"api/[controller]"` при применении `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="17c28-784">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="17c28-785">Настройка маршрутов на основе атрибутов с помощью модели приложения</span><span class="sxs-lookup"><span data-stu-id="17c28-785">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="17c28-786">*Модель приложения* — это объектная модель, которая создается при запуске со всеми метаданными, используемыми платформой MVC для маршрутизации и выполнения действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-786">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="17c28-787">*Модель приложения* включает в себя все данные, собранные из атрибутов маршрутов (посредством интерфейса `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="17c28-787">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="17c28-788">Вы можете создать *соглашения*, чтобы изменить модель приложения при запуске с целью настройки маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-788">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="17c28-789">В этом разделе приводится простой пример настройки маршрутизации с помощью модели приложения.</span><span class="sxs-lookup"><span data-stu-id="17c28-789">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="17c28-790">Смешанная маршрутизация с помощью атрибутов и на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="17c28-790">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="17c28-791">В приложениях MVC маршрутизация с помощью атрибутов и маршрутизация на основе соглашений могут использоваться вместе.</span><span class="sxs-lookup"><span data-stu-id="17c28-791">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="17c28-792">Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.</span><span class="sxs-lookup"><span data-stu-id="17c28-792">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="17c28-793">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-793">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="17c28-794">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="17c28-794">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="17c28-795">Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="17c28-795">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="17c28-796">**Любой** атрибут маршрута контроллера делает все действия в атрибуте контроллера маршрутизируемыми.</span><span class="sxs-lookup"><span data-stu-id="17c28-796">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-797">Два типа систем маршрутизации отличает процесс, выполняемый после нахождения URL-адреса, соответствующего шаблону маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-797">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="17c28-798">При маршрутизации на основе соглашений значения маршрута из соответствия используются для выбора действия и контроллера из таблицы подстановки, содержащей все действия с маршрутизацией на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="17c28-798">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="17c28-799">При маршрутизации с помощью атрибутов каждый шаблон уже связан с действием, и дальнейшая подстановка не требуется.</span><span class="sxs-lookup"><span data-stu-id="17c28-799">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="17c28-800">Сложные сегменты</span><span class="sxs-lookup"><span data-stu-id="17c28-800">Complex segments</span></span>

<span data-ttu-id="17c28-801">Сложные сегменты (например, `[Route("/dog{token}cat")]`) обрабатываются путем "нежадного" сопоставления литералов справа налево.</span><span class="sxs-lookup"><span data-stu-id="17c28-801">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="17c28-802">Описание см. в [исходном коде](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="17c28-802">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="17c28-803">Дополнительные сведения см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="17c28-803">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="17c28-804">Формирование URL-адреса</span><span class="sxs-lookup"><span data-stu-id="17c28-804">URL Generation</span></span>

<span data-ttu-id="17c28-805">Приложения MVC могут использовать функции формирования URL-адреса, предоставляемые системой маршрутизации, для создания URL-ссылок на действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-805">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="17c28-806">Формирование URL-адресов устраняет необходимость в их жестком задании, что делает код надежнее и проще в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="17c28-806">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="17c28-807">В этом разделе рассматриваются функции формирования URL-адреса, предоставляемые платформой MVC, и описываются лишь базовые принципы их работы.</span><span class="sxs-lookup"><span data-stu-id="17c28-807">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="17c28-808">Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="17c28-808">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="17c28-809">Интерфейс `IUrlHelper` — это базовый компонент инфраструктуры, обеспечивающий взаимодействие между MVC и системой маршрутизации для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="17c28-809">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="17c28-810">Доступ к экземпляру `IUrlHelper` в контроллерах, представлениях и компонентах представлений можно получить посредством свойства `Url`.</span><span class="sxs-lookup"><span data-stu-id="17c28-810">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="17c28-811">В этом примере интерфейс `IUrlHelper` используется посредством свойства `Controller.Url` для формирования URL-адреса другого действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-811">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="17c28-812">Если в приложении применяется маршрут по умолчанию на основе соглашения, значением переменной `url` будет строка с путем URL-адреса `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="17c28-812">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="17c28-813">Этот путь URL-адреса создается системой маршрутизации путем объединения значений маршрута из текущего запроса (значения окружения) со значениями, переданными в `Url.Action`, и подстановки этих значений в шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-813">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="17c28-814">Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения.</span><span class="sxs-lookup"><span data-stu-id="17c28-814">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="17c28-815">Параметр маршрута, у которого нет значения, может принимать значение по умолчанию, если оно имеется, или пропускаться, если он является необязательным (как в случае с параметром `id` в этом примере).</span><span class="sxs-lookup"><span data-stu-id="17c28-815">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="17c28-816">Сформировать URL-адрес не удастся, если у любого из обязательных параметров маршрута не будет соответствующего значения.</span><span class="sxs-lookup"><span data-stu-id="17c28-816">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="17c28-817">Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.</span><span class="sxs-lookup"><span data-stu-id="17c28-817">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="17c28-818">В примере `Url.Action` выше предполагается использование маршрутизации на основе соглашений, однако формирование URL-адреса происходит аналогичным образом и в случае с маршрутизацией с помощью атрибутов, хотя принципы иные.</span><span class="sxs-lookup"><span data-stu-id="17c28-818">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="17c28-819">При применении маршрутизации на основе соглашений шаблон расширяется с помощью значений маршрута, и значения маршрута для `controller` и `action` обычно находятся в этом шаблоне. Это возможно по той причине, что URL-адреса, сопоставленные системой маршрутизации, следуют *соглашению*.</span><span class="sxs-lookup"><span data-stu-id="17c28-819">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="17c28-820">При маршрутизации с помощью атрибутов значения маршрута для `controller` и `action` не могут находиться в шаблоне. Вместо этого они применяются для поиска нужного шаблона.</span><span class="sxs-lookup"><span data-stu-id="17c28-820">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="17c28-821">В этом примере используется маршрутизация с помощью атрибутов:</span><span class="sxs-lookup"><span data-stu-id="17c28-821">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="17c28-822">MVC создает таблицу подстановки, содержащую все действия с маршрутизацией с помощью атрибутов, и сопоставляет значения `controller` и `action` для выбора шаблона маршрута, с помощью которого должен формироваться URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="17c28-822">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="17c28-823">В приведенном выше примере создается URL-адрес `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="17c28-823">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="17c28-824">Формирование URL-адресов по имени действия</span><span class="sxs-lookup"><span data-stu-id="17c28-824">Generating URLs by action name</span></span>

<span data-ttu-id="17c28-825">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="17c28-825">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="17c28-826">`Action`) и все связанные перегрузки предполагают необходимость указания целевого объекта путем задания имени контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-826">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-827">При использовании метода `Url.Action` текущие значения маршрута для `controller` и `action` уже определены: значения `controller` и `action` включены как в *значения окружения, так и в* **обычные** *значения*.</span><span class="sxs-lookup"><span data-stu-id="17c28-827">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="17c28-828">Метод `Url.Action` всегда использует текущие значения `action` и `controller` и формирует путь URL-адреса, ведущий к текущему действию.</span><span class="sxs-lookup"><span data-stu-id="17c28-828">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="17c28-829">При формировании URL-адреса система маршрутизации пытается использовать значения окружения для заполнения сведений, которые вы не предоставили.</span><span class="sxs-lookup"><span data-stu-id="17c28-829">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="17c28-830">При использовании такого маршрута, как `{a}/{b}/{c}/{d}`, и значений окружения `{ a = Alice, b = Bob, c = Carol, d = David }` система маршрутизации имеет достаточно информации для формирования URL-адреса без дополнительных значений, так как все параметры маршрута имеют значения.</span><span class="sxs-lookup"><span data-stu-id="17c28-830">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="17c28-831">Если добавить значение `{ d = Donovan }`, значение `{ d = David }` не будет учитываться, и будет сформирован путь URL-адреса `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="17c28-831">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="17c28-832">Пути URL-адресов являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="17c28-832">URL paths are hierarchical.</span></span> <span data-ttu-id="17c28-833">Если в приведенном выше примере добавить значение `{ c = Cheryl }`, пропущены будут оба значения `{ c = Carol, d = David }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-833">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="17c28-834">В этом случае параметр `d` больше не имеет значения и сформировать URL-адрес не удастся.</span><span class="sxs-lookup"><span data-stu-id="17c28-834">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="17c28-835">Потребуется указать нужные значения для параметров `c` и `d`.</span><span class="sxs-lookup"><span data-stu-id="17c28-835">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="17c28-836">Эта проблема может возникнуть с маршрутом по умолчанию (`{controller}/{action}/{id?}`), но на практике она встречается редко, так как `Url.Action` всегда явным образом задает значения `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="17c28-836">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="17c28-837">Более длинные перегрузки `Url.Action` также принимают дополнительный объект *значений маршрута* для предоставления значений параметров маршрута, отличных от `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="17c28-837">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="17c28-838">Чаще всего он применяется с параметром `id`, например `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="17c28-838">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="17c28-839">В соответствии с соглашением объект *значений маршрута* обычно имеет анонимный тип, но это может быть также экземпляр `IDictionary<>` или *обычный объект .NET*.</span><span class="sxs-lookup"><span data-stu-id="17c28-839">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="17c28-840">Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="17c28-840">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="17c28-841">Чтобы создать абсолютный URL-адрес, используйте перегрузку, принимающую `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="17c28-841">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="17c28-842">Формирование URL-адресов по маршруту</span><span class="sxs-lookup"><span data-stu-id="17c28-842">Generating URLs by route</span></span>

<span data-ttu-id="17c28-843">В приведенном выше коде демонстрировалось формирование URL-адреса путем передачи имен контроллера и действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-843">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="17c28-844">Интерфейс `IUrlHelper` также предоставляет семейство методов `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="17c28-844">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="17c28-845">Эти методы похожи на `Url.Action`, но они не копируют текущие значения `action` и `controller` в значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-845">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="17c28-846">Наиболее распространенный способ их применения — указание имени определенного маршрута, который должен использоваться для формирования URL-адреса, как правило, *без* указания имени контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-846">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="17c28-847">Формирование URL-адресов в HTML</span><span class="sxs-lookup"><span data-stu-id="17c28-847">Generating URLs in HTML</span></span>

<span data-ttu-id="17c28-848">Интерфейс `IHtmlHelper` предоставляет методы `HtmlHelper``Html.BeginForm` и `Html.ActionLink` для создания элементов `<form>` и `<a>` соответственно.</span><span class="sxs-lookup"><span data-stu-id="17c28-848">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="17c28-849">Эти методы используют метод `Url.Action` для формирования URL-адреса и принимают одинаковые аргументы.</span><span class="sxs-lookup"><span data-stu-id="17c28-849">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="17c28-850">Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.</span><span class="sxs-lookup"><span data-stu-id="17c28-850">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="17c28-851">Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`.</span><span class="sxs-lookup"><span data-stu-id="17c28-851">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="17c28-852">Обе они реализуются с помощью интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="17c28-852">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="17c28-853">Дополнительные сведения см. в статье [Работа с формами](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="17c28-853">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="17c28-854">Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.</span><span class="sxs-lookup"><span data-stu-id="17c28-854">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="17c28-855">Формирование URL-адресов в результатах действий</span><span class="sxs-lookup"><span data-stu-id="17c28-855">Generating URLS in Action Results</span></span>

<span data-ttu-id="17c28-856">В предыдущих примерах было продемонстрировано использование интерфейса `IUrlHelper` в контроллере, хотя чаще всего URL-адреса формируются в контроллерах в рамках результата действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-856">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="17c28-857">Базовые классы `ControllerBase` и `Controller` предоставляют удобные методы для результатов действий, ссылающихся на другое действие.</span><span class="sxs-lookup"><span data-stu-id="17c28-857">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="17c28-858">Одним из типичных способов их применения является перенаправление после принятия входных данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="17c28-858">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="17c28-859">Фабричные методы результатов действий по своей структуре похожи на методы интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="17c28-859">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="17c28-860">Выделенные маршруты на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="17c28-860">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="17c28-861">При маршрутизации на основе соглашений может использоваться особый тип определения маршрута — *выделенный маршрут на основе соглашения*.</span><span class="sxs-lookup"><span data-stu-id="17c28-861">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="17c28-862">В приведенном ниже примере маршрут с именем `blog` является выделенным маршрутом на основе соглашения.</span><span class="sxs-lookup"><span data-stu-id="17c28-862">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="17c28-863">При использовании таких определений маршрутов метод `Url.Action("Index", "Home")` создаст путь URL-адреса `/` с маршрутом `default`. В чем же причина?</span><span class="sxs-lookup"><span data-stu-id="17c28-863">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="17c28-864">Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="17c28-864">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="17c28-865">В случае с выделенными маршрутами на основе соглашений используется особое поведение значений по умолчанию, для которых нет соответствующих параметров маршрута. Благодаря ему маршрут не может быть слишком универсальным при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-865">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="17c28-866">В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет.</span><span class="sxs-lookup"><span data-stu-id="17c28-866">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="17c28-867">Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="17c28-867">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="17c28-868">Сформировать URL-адрес с помощью `blog` не удастся, так как значения `{ controller = Home, action = Index }` не соответствуют `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-868">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="17c28-869">После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="17c28-869">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="17c28-870">Области</span><span class="sxs-lookup"><span data-stu-id="17c28-870">Areas</span></span>

<span data-ttu-id="17c28-871">[Области](areas.md) — это возможность MVC, которая служит для объединения связанных функций в группу в виде отдельного пространства имен маршрутизации (для действий контроллеров) и структуры папок (для представлений).</span><span class="sxs-lookup"><span data-stu-id="17c28-871">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="17c28-872">Благодаря областям приложение может иметь несколько контроллеров с одинаковыми именами, если у них будут разные *области*.</span><span class="sxs-lookup"><span data-stu-id="17c28-872">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="17c28-873">При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`.</span><span class="sxs-lookup"><span data-stu-id="17c28-873">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="17c28-874">В этом разделе рассматривается взаимодействие системы маршрутизации с областями. Подробные сведения об использовании областей с представлениями см. в статье [Области](areas.md).</span><span class="sxs-lookup"><span data-stu-id="17c28-874">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="17c28-875">В следующем примере в MVC настраивается использование маршрута на основе соглашения по умолчанию и *маршрута области* для области с именем `Blog`:</span><span class="sxs-lookup"><span data-stu-id="17c28-875">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="17c28-876">Результатом сопоставления первого маршрута с таким путем URL-адреса, как `/Manage/Users/AddUser`, будут значения маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-876">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="17c28-877">Значение маршрута `area` получается на основе значения по умолчанию для параметра `area`. По сути, маршрут, создаваемый методом `MapAreaRoute`, эквивалентен следующему:</span><span class="sxs-lookup"><span data-stu-id="17c28-877">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="17c28-878">Метод `MapAreaRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`).</span><span class="sxs-lookup"><span data-stu-id="17c28-878">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="17c28-879">Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-879">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="17c28-880">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="17c28-880">Conventional routing is order-dependent.</span></span> <span data-ttu-id="17c28-881">Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.</span><span class="sxs-lookup"><span data-stu-id="17c28-881">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="17c28-882">В предыдущем примере значения маршрута будут соответствовать следующему действию:</span><span class="sxs-lookup"><span data-stu-id="17c28-882">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="17c28-883">`AreaAttribute` обозначает контроллер в рамках области. В таком случае говорят, что контроллер находится в области `Blog`.</span><span class="sxs-lookup"><span data-stu-id="17c28-883">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="17c28-884">Контроллеры без атрибута `[Area]` не входят ни в одну область и **не** будут соответствовать маршруту, если система маршрутизации предоставляет значение маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="17c28-884">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="17c28-885">В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="17c28-885">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="17c28-886">Пространство имен каждого контроллера приведено здесь для полноты. В противном случае между контроллерами возник бы конфликт именования, и произошла бы ошибка компилятора.</span><span class="sxs-lookup"><span data-stu-id="17c28-886">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="17c28-887">Имена пространств классов не влияют на маршрутизацию в MVC.</span><span class="sxs-lookup"><span data-stu-id="17c28-887">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="17c28-888">Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="17c28-888">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="17c28-889">Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="17c28-889">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-890">В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.</span><span class="sxs-lookup"><span data-stu-id="17c28-890">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="17c28-891">При выполнении действия внутри области значение маршрута для `area` будет доступно как *значение окружения*, которое система маршрутизации использует для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="17c28-891">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="17c28-892">Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="17c28-892">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="17c28-893">Основные сведения об интерфейсе IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="17c28-893">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="17c28-894">В этом разделе описываются внутренние принципы работы платформы и то, как MVC выбирает действие, которое необходимо выполнить.</span><span class="sxs-lookup"><span data-stu-id="17c28-894">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="17c28-895">Типичному приложению пользовательская реализация `IActionConstraint` не требуется.</span><span class="sxs-lookup"><span data-stu-id="17c28-895">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="17c28-896">Скорее всего, вы уже пользовались интерфейсом `IActionConstraint`, даже если не знакомы с ним.</span><span class="sxs-lookup"><span data-stu-id="17c28-896">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="17c28-897">Атрибут `[HttpGet]` и сходные атрибуты `[Http-VERB]` реализуют интерфейс `IActionConstraint`, чтобы ограничить выполнение метода действия.</span><span class="sxs-lookup"><span data-stu-id="17c28-897">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="17c28-898">В случае с маршрутом по умолчанию на основе соглашения путь URL-адреса `/Products/Edit` даст значения `{ controller = Products, action = Edit }`, которые будут соответствовать **обоим** приведенным здесь действиям.</span><span class="sxs-lookup"><span data-stu-id="17c28-898">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="17c28-899">В терминологии `IActionConstraint` можно сказать, что оба действия считаются кандидатами, так как они оба соответствуют данным маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-899">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="17c28-900">Когда метод `HttpGetAttribute` выполняется, он сообщает, что *Edit()* является соответствием для запроса *GET*, но не является соответствием для каких-либо иных HTTP-команд.</span><span class="sxs-lookup"><span data-stu-id="17c28-900">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="17c28-901">Для действия `Edit(...)` ограничения не определены, поэтому оно соответствует любой HTTP-команде.</span><span class="sxs-lookup"><span data-stu-id="17c28-901">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="17c28-902">Поэтому если рассматривать запрос `POST`, соответствием будет только `Edit(...)`.</span><span class="sxs-lookup"><span data-stu-id="17c28-902">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="17c28-903">Однако в случае с запросом `GET` соответствовать могут оба действия, хотя действие с `IActionConstraint` всегда считается *имеющим приоритет*.</span><span class="sxs-lookup"><span data-stu-id="17c28-903">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="17c28-904">Таким образом, поскольку действие `Edit()` имеет атрибут `[HttpGet]`, оно считается более конкретным и будет выбрано в случае соответствия обоих действий.</span><span class="sxs-lookup"><span data-stu-id="17c28-904">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="17c28-905">По сути, интерфейс `IActionConstraint` представляет собой своего рода *перегрузку*, но вместо перегрузки методов с одинаковыми именами он перегружает действия, соответствующие одному и тому же URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="17c28-905">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="17c28-906">При маршрутизации с помощью атрибутов также применяется интерфейс `IActionConstraint`, в результате чего кандидатами могут считаться действия из разных контроллеров.</span><span class="sxs-lookup"><span data-stu-id="17c28-906">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="17c28-907">Реализация IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="17c28-907">Implementing IActionConstraint</span></span>

<span data-ttu-id="17c28-908">Самый простой способ реализовать интерфейс `IActionConstraint` — создать класс, производный от `System.Attribute`, и добавить его в действия и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="17c28-908">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="17c28-909">MVC автоматически обнаруживает экземпляры `IActionConstraint`, применяемые как атрибуты.</span><span class="sxs-lookup"><span data-stu-id="17c28-909">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="17c28-910">Вы можете использовать модель приложения для применения ограничений, и это, пожалуй, самый гибкий подход, так как он позволяет метапрограммировать способ их применения.</span><span class="sxs-lookup"><span data-stu-id="17c28-910">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="17c28-911">В следующем примере ограничение выбирает действие на основе *кода страны* из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="17c28-911">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="17c28-912">[Полный пример можно найти в GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="17c28-912">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="17c28-913">Вы отвечаете за реализацию метода `Accept` и определение очередности применения ограничений.</span><span class="sxs-lookup"><span data-stu-id="17c28-913">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="17c28-914">В этом случае метод `Accept` возвращает значение `true`, означающее, что действие является соответствующим при соответствии значения маршрута `country`.</span><span class="sxs-lookup"><span data-stu-id="17c28-914">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="17c28-915">Таким образом, он позволяет возвращаться к действию без атрибута, чем отличается от метода `RouteValueAttribute`.</span><span class="sxs-lookup"><span data-stu-id="17c28-915">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="17c28-916">В примере показано, что если определено действие `en-US`, то для кода страны `fr-FR` будет выбран более общий контроллер, к которому не применяется ограничение `[CountrySpecific(...)]`.</span><span class="sxs-lookup"><span data-stu-id="17c28-916">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="17c28-917">Свойство `Order` определяет, к какому *этапу* относится ограничение.</span><span class="sxs-lookup"><span data-stu-id="17c28-917">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="17c28-918">Ограничения действий применяются группами в соответствии со значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="17c28-918">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="17c28-919">Например, все предоставляемые платформой атрибуты HTTP-методов имеют одинаковое значение `Order`, благодаря чему они выполняются на одном этапе.</span><span class="sxs-lookup"><span data-stu-id="17c28-919">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="17c28-920">Чтобы реализовать требуемые политики, можно использовать любое количество этапов.</span><span class="sxs-lookup"><span data-stu-id="17c28-920">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="17c28-921">Чтобы выбрать значение `Order`, подумайте, должно ли ограничение применяться перед выполнением HTTP-методов.</span><span class="sxs-lookup"><span data-stu-id="17c28-921">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="17c28-922">Чем меньше значение, тем раньше применяется ограничение.</span><span class="sxs-lookup"><span data-stu-id="17c28-922">Lower numbers run first.</span></span>

::: moniker-end
